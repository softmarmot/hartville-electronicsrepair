<script setup lang="ts">
const props = defineProps<{
  phone?: string          // e.g. "(330) 555-1234"
  phoneHref?: string      // e.g. "tel:+13305551234"
  email?: string          // e.g. "info@mydomain.com"
  emailHref?: string      // e.g. "mailto:info@mydomain.com"
  serviceArea?: string    // e.g. "Hartville, OH + surrounding areas"
  hours?: string          // e.g. "Mon–Sun • 9:00 AM – 8:00 PM"
}>()

const year = new Date().getFullYear()
</script>

<template>
  <footer id="footer">
    <div class="mx-auto max-w-6xl px-6 py-12">
      <div class="grid gap-10 md:grid-cols-2">
        <!-- Brand -->
        <div class="space-y-3">
          <div class="leading-tight">
            <div class="text-lg font-light text-zinc-300 -mt-1">Hartville</div>
            <div class="text-3xl font-light">Electronics Repair</div>
          </div>

          <p class="text-sm text-zinc-300 leading-6">
            Mobile electronics repair — phones, tablets, laptops, consoles, TVs.
            <span class="text-zinc-200">By appointment only.</span>
          </p>

          <div class="pt-2">
            <a
              class="inline-flex items-center gap-2 rounded-lg bg-yellow-400 px-4 py-2 text-sm font-medium text-zinc-900 hover:opacity-90 transition"
              :href="props.phoneHref || 'tel:+1XXXXXXXXXX'"
            >
              <span class="material-symbols-outlined text-[20px]" aria-hidden="true">call</span>
              Call / Text
            </a>
          </div>
        </div>

        <!-- Contacts -->
        <div>
          <h4 class="text-xl font-light mb-4">Contact</h4>

          <div class="space-y-4 text-zinc-300">
            <div class="flex gap-3">
              <span class="material-symbols-outlined text-yellow-400" aria-hidden="true">call</span>
              <div>
                <div class="text-sm text-zinc-400">Phone / Text</div>
                <a
                  class="hover:text-white transition"
                  :href="props.phoneHref || 'tel:+1XXXXXXXXXX'"
                >
                  {{ props.phone || '(XXX) XXX-XXXX' }}
                </a>
              </div>
            </div>

            <div class="flex gap-3">
              <span class="material-symbols-outlined text-yellow-400">alternate_email</span>
              <div>
                <div class="text-sm text-zinc-400">Email</div>
                <a
                  class="hover:text-white transition"
                  :href="props.emailHref || 'mailto:info@mydomain.com'"
                >
                  {{ props.email || 'info@mydomain.com' }}
                </a>
              </div>
            </div>

            <div class="flex gap-3">
              <span class="material-symbols-outlined text-yellow-400" aria-hidden="true">schedule</span>
              <div>
                <div class="text-sm text-zinc-400">Hours</div>
                <div>{{ props.hours || 'Mon–Sun • By appointment' }}</div>
              </div>
            </div>

            <div class="flex gap-3">
              <span class="material-symbols-outlined text-yellow-400" aria-hidden="true">location_on</span>
              <div>
                <div class="text-sm text-zinc-400">Service Area</div>
                <div>{{ props.serviceArea || 'Hartville, OH + surrounding areas' }}</div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="text-xs text-zinc-400 leading-5 pt-2 mt-6">
        Note: We are a <span class="text-zinc-300">mobile repair service</span> and do not accept walk-ins.
        Contact us to schedule a pickup/drop-off or appointment.
      </div>

      <div class="mt-6 border-t border-zinc-700 pt-6 flex flex-col gap-2 md:flex-row md:items-center md:justify-between">
        <div class="text-xs text-zinc-400">
          © {{ year }} Hartville Electronics Repair. All rights reserved.
        </div>

        <div class="text-xs text-zinc-500">
          Built with fair prices • free diagnostics • warranty-backed repairs
        </div>
      </div>
    </div>
  </footer>
</template>
