<script setup lang="ts">
import {computed, reactive, ref} from 'vue'

type Field = 'name' | 'phone' | 'email' | 'message'

// Google Form: "Hartville Electronics Repair Contact Form"
const GOOGLE_FORM_ACTION = 'https://docs.google.com/forms/d/e/1FAIpQLSdWTwVuJPgx39NkUJbMnFcFdNMl-u7xhd3KJhvgDUPtl__YHQ/formResponse'
const GOOGLE_FORM_FIELDS: Record<Field, string> = {
  name: 'entry.2005620554',
  email: 'entry.1045781291',
  phone: 'entry.1166974658',
  message: 'entry.839337160',
}

const EMAIL_RE = /^[^\s@]+@[^\s@]+\.[^\s@]{2,}$/

const validators: Record<Field, (value: string) => string> = {
  name(value) {
    if (!value) return 'Please enter your name.'
    if (value.length < 2) return 'Name must be at least 2 characters.'
    if (value.length > 100) return 'Name must be 100 characters or fewer.'
    return ''
  },
  phone(value) {
    if (!value) return 'Please enter your phone number.'
    if (value.replace(/\D/g, '').length !== 10) return 'Please enter a full 10-digit phone number.'
    return ''
  },
  email(value) {
    if (!value) return 'Please enter your email.'
    if (!EMAIL_RE.test(value)) return 'Please enter a valid email address.'
    return ''
  },
  message(value) {
    if (!value) return 'Please tell us about your device and the issue.'
    if (value.length < 10) return 'Message must be at least 10 characters.'
    if (value.length > 2000) return 'Message must be 2000 characters or fewer.'
    return ''
  },
}

const values = reactive<Record<Field, string>>({name: '', phone: '', email: '', message: ''})
const touched = reactive<Record<Field, boolean>>({name: false, phone: false, email: false, message: false})
const status = ref<'idle' | 'sending' | 'success' | 'error'>('idle')

const errors = computed(() => {
  const result = {} as Record<Field, string>
  for (const field of Object.keys(validators) as Field[]) {
    result[field] = validators[field](values[field].trim())
  }
  return result
})

// Errors appear once a field has been left (or on submit), then update live while typing
function errorFor(field: Field) {
  return touched[field] ? errors.value[field] : ''
}

function fieldClass(field: Field) {
  return [
    'w-full rounded-lg border bg-zinc-900 px-3 py-2 text-zinc-100 placeholder:text-zinc-500 outline-none transition',
    // Browsers ignore background-color on autofilled fields (password managers, saved data);
    // paint over their light-blue highlight with an inset shadow to keep the dark theme
    'autofill:shadow-[inset_0_0_0_1000px_var(--color-zinc-900)] autofill:[-webkit-text-fill-color:var(--color-zinc-100)]',
    errorFor(field) ? 'border-red-400 focus:border-red-400' : 'border-zinc-700 focus:border-yellow-400',
  ]
}

// Keeps up to 10 digits, dropping a leading US country code "1" (area codes never start with 1),
// e.g. pasted "+1 330-958-3587" -> "3309583587"
function phoneDigits(value: string) {
  return value.replace(/\D/g, '').replace(/^1/, '').slice(0, 10)
}

// Formats progressively so backspacing never gets stuck on a separator:
// "330" -> "(330", "330958" -> "(330) 958", "3309583587" -> "(330) 958-3587"
function formatPhone(digits: string) {
  if (!digits) return ''
  if (digits.length <= 3) return `(${digits}`
  if (digits.length <= 6) return `(${digits.slice(0, 3)}) ${digits.slice(3)}`
  return `(${digits.slice(0, 3)}) ${digits.slice(3, 6)}-${digits.slice(6)}`
}

function onPhoneInput(event: Event) {
  const input = event.target as HTMLInputElement
  const caret = input.selectionStart ?? input.value.length
  const digitsBeforeCaret = phoneDigits(input.value.slice(0, caret)).length

  const formatted = formatPhone(phoneDigits(input.value))
  values.phone = formatted
  // Set directly too: if the formatted value didn't change (e.g. a letter was typed), Vue won't re-render
  input.value = formatted

  // Put the caret back after the same number of digits it was after before formatting
  let position = 0
  for (let seen = 0; position < formatted.length && seen < digitsBeforeCaret; position++) {
    if (/\d/.test(formatted[position]!)) seen++
  }
  input.setSelectionRange(position, position)
}

function resetForm(form: HTMLFormElement) {
  // Native reset also clears the browser's autofill state; setting values from Vue alone doesn't
  form.reset()
  // 1Password marks filled fields with this attribute (its stylesheet tints them blue) and only
  // removes it when the user types, so clear it ourselves
  form.querySelectorAll('[data-com-onepassword-filled]').forEach((el) => {
    el.removeAttribute('data-com-onepassword-filled')
  })
  for (const field of Object.keys(values) as Field[]) {
    values[field] = ''
    touched[field] = false
  }
}

async function onSubmit(event: Event) {
  const form = event.target as HTMLFormElement
  const fields = Object.keys(values) as Field[]
  fields.forEach((field) => (touched[field] = true))

  const firstInvalid = fields.find((field) => errors.value[field])
  if (firstInvalid) {
    form.querySelector<HTMLElement>(`[name="${firstInvalid}"]`)?.focus()
    return
  }

  const body = new URLSearchParams()
  for (const field of fields) {
    body.append(GOOGLE_FORM_FIELDS[field], values[field].trim())
  }

  status.value = 'sending'
  try {
    // Google Forms doesn't send CORS headers, so the response is opaque;
    // a resolved request means it reached Google.
    await fetch(GOOGLE_FORM_ACTION, {method: 'POST', mode: 'no-cors', body})
    status.value = 'success'
    resetForm(form)
  } catch {
    status.value = 'error'
  }
}
</script>

<template>
  <form class="space-y-4" novalidate @submit.prevent="onSubmit">
    <div class="space-y-1">
      <div class="grid gap-y-1 gap-x-2 sm:grid-cols-2">
        <div>
          <input
            v-model="values.name"
            type="text"
            name="name"
            autocomplete="name"
            placeholder="Your name"
            aria-label="Your name"
            maxlength="100"
            :aria-invalid="!!errorFor('name')"
            aria-describedby="contact-name-error"
            :class="fieldClass('name')"
            class="mt-1"
            @blur="touched.name = true"
          />
          <p v-if="errorFor('name')" id="contact-name-error" class="mt-1 text-xs text-red-400">
            {{ errorFor('name') }}
          </p>
        </div>

        <div>
          <input
            :value="values.phone"
            type="tel"
            name="phone"
            autocomplete="tel"
            placeholder="(XXX) XXX-XXXX"
            aria-label="Phone number"
            :aria-invalid="!!errorFor('phone')"
            aria-describedby="contact-phone-error"
            :class="fieldClass('phone')"
            class="mt-1"
            @input="onPhoneInput"
            @blur="touched.phone = true"
          />
          <p v-if="errorFor('phone')" id="contact-phone-error" class="mt-1 text-xs text-red-400">
            {{ errorFor('phone') }}
          </p>
        </div>
      </div>

      <div>
        <input
          v-model="values.email"
          type="email"
          name="email"
          autocomplete="email"
          placeholder="you@example.com"
          aria-label="Email"
          maxlength="254"
          :aria-invalid="!!errorFor('email')"
          aria-describedby="contact-email-error"
          :class="fieldClass('email')"
          class="mt-1"
          @blur="touched.email = true"
        />
        <p v-if="errorFor('email')" id="contact-email-error" class="mt-1 text-xs text-red-400">
          {{ errorFor('email') }}
        </p>
      </div>

      <div>
        <textarea
          v-model="values.message"
          name="message"
          rows="4"
          placeholder="Tell us about your device and the issue"
          aria-label="Message"
          maxlength="2000"
          :aria-invalid="!!errorFor('message')"
          aria-describedby="contact-message-error"
          :class="fieldClass('message')"
          class="mt-1 block resize-y"
          @blur="touched.message = true"
        ></textarea>
        <p v-if="errorFor('message')" id="contact-message-error" class="mt-1 text-xs text-red-400">
          {{ errorFor('message') }}
        </p>
      </div>
    </div>

    <button
      type="submit"
      :disabled="status === 'sending'"
      class="hover:cursor-pointer inline-flex w-full sm:w-auto items-center justify-center gap-2 rounded-lg bg-yellow-400 px-4 py-2 text-sm font-medium text-zinc-900 hover:opacity-90 transition disabled:opacity-60 disabled:cursor-not-allowed"
    >
      <span class="material-symbols-outlined text-[20px]" aria-hidden="true">send</span>
      {{ status === 'sending' ? 'Sending…' : 'Send Message' }}
    </button>

    <p v-if="status === 'success'" class="text-sm text-zinc-300" role="status">
      Thanks! Your message has been sent — we'll get back to you soon.
    </p>
    <p v-else-if="status === 'error'" class="text-sm text-red-400" role="alert">
      Something went wrong. Please try again or call / text us directly.
    </p>
  </form>
</template>
