<!-- eslint-disable vue/no-mutating-props -->
<template>
  <v-card height="100%">
    <v-card-text>
      <ProofImageCropped class="mb-4" height="200px" :proofImageFilePath="productPriceForm.proofImage" :boundingBox="productPriceForm.bounding_box" />
      <v-row v-if="showProductNameField">
        <v-col>
          <v-text-field
            :model-value="productPriceForm.product_name"
            :label="$t('Common.ProductName')"
            type="text"
            hide-details="auto"
          />
        </v-col>
      </v-row>
      <ProductInputRow :productForm="productPriceForm" :disableInitWhenSwitchingType="true" :hideProductBarcode="false" :hideBarcodeScannerTab="true" @filled="productFormFilled = $event" />
      <PriceInputRow class="mt-0" :priceForm="productPriceForm" :product="productPriceForm.product" :hideCurrencyChoice="true" @filled="pricePriceFormFilled = $event" />
    </v-card-text>
    <v-divider v-if="mode === 'Validation'" />
    <v-card-text v-if="mode === 'Validation'">
      <ProofFooterRow :proof="productPriceForm.proof" :showProofChip="true" :hideProofType="true" :hideProofActions="true" :readonly="true" />
    </v-card-text>
    <v-divider />
    <v-card-actions v-if="mode === 'Contribution'">
      <v-btn
        color="error"
        variant="outlined"
        prepend-icon="mdi-delete"
        @click="removePriceTag"
      >
        {{ $t('Common.Delete') }}
      </v-btn>
    </v-card-actions>
    <v-card-actions v-else-if="mode === 'Validation'">
      <v-btn
        color="error"
        variant="outlined"
        @click="removePriceTag(3)"
      >
        {{ $t('Common.Error') }}
      </v-btn>
      <v-btn
        color="warning"
        variant="outlined"
        @click="removePriceTag(2)"
      >
        {{ $t('Common.Unreadable') }}
      </v-btn>
      <v-spacer />
      <v-btn
        color="success"
        variant="flat"
        @click="validatePriceTag"
      >
        {{ $t('Common.Upload') }}
      </v-btn>
    </v-card-actions>
  </v-card>
</template>


<script>
import { defineAsyncComponent } from 'vue'
import constants from '../constants'

export default {
  components: {
    ProofImageCropped: defineAsyncComponent(() => import('../components/ProofImageCropped.vue')),
  ProductInputRow: defineAsyncComponent(() => import('../components/ProductInputRow.vue')),
  PriceInputRow: defineAsyncComponent(() => import('../components/PriceInputRow.vue')),
  ProofFooterRow: defineAsyncComponent(() => import('../components/ProofFooterRow.vue')),
  },
  props: {
    productPriceForm: {
      type: Object,
      default: () => ({
        id: null,
        type: null,
        category_tag: null,
        origins_tags: '',
        labels_tags: [],
        price: null,
        price_per: null,
        price_is_discounted: false,
        price_without_discount: null,
        currency: null,
        proofImage: null,
        processed: null,
        detected_product_code: null,
        product_name: null,
      })
    },
    mode: {
      type: String,
      default: 'Contribution'  // or 'Validation'
    },
    showProductNameField: {
      type: Boolean,
      default: false
    }
  },
  emits: ['removePriceTag', 'validatePriceTag'],
  data() {
    return {
      productFormFilled: false,
      pricePriceFormFilled: false,
    }
  },
  computed: {
    productIsTypeProduct() {
      return this.productPriceForm.type === constants.PRICE_TYPE_PRODUCT
    }
  },
  methods: {
    removePriceTag(status=null) {
      this.$emit('removePriceTag', status)
    },
    validatePriceTag() {
      this.$emit('validatePriceTag')
    }
  }
}
</script>
