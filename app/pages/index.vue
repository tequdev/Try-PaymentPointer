<template>
  <v-container fluid>
    <v-row justify="center">
      <v-col sm="10" md="10" lg="8" xl="5" class="my-12">
        <v-card height="300" class="pa-3 pa-sm-5 pa-md-10">
          <v-spacer />
          <v-text-field
            v-model="address"
            class="my-5"
            hide-details=""
            label="Your PaymentPointer"
          />
          <v-spacer />
          <v-toolbar flat>
            <v-spacer />
            <v-btn depressed outlined>
              <span id="total">No Monetization</span>
              <span id="currency"></span>
            </v-btn>
            <v-spacer />
          </v-toolbar>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import { Vue, Component } from 'nuxt-property-decorator'

@Component({})
export default class extends Vue {
  address: string = ''
  total = 0
  scale?: number

  head() {
    return {
      meta: [
        {
          name: 'monetization',
          content: this.paymentpointer(),
        },
      ],
    }
  }

  paymentpointer() {
    return this.address
  }

  mounted() {
    if ((document as any).monetization) {
      ;(document as any).monetization.addEventListener(
        'monetizationprogress',
        this.monetizeEvent
      )
    }
  }

  destroyed() {
    if ((document as any).monetization) {
      ;(document as any).monetization.removeEventListener(
        'monetizationprogress',
        this.monetizeEvent
      )
    }
  }

  monetizeEvent(ev: any) {
    // initialize currency and scale on first progress event
    if (this.total === 0) {
      this.scale = ev.detail.assetScale
      document.getElementById('currency')!.textContent = ev.detail.assetCode
    }

    this.total += Number(ev.detail.amount)

    const formatted = (this.total * Math.pow(10, -1 * this.scale!)).toFixed(
      this.scale
    )
    document.getElementById('total')!.textContent = formatted
  }
}
</script>
