<!--
   The MIT License
   Copyright (c) 2019- Nordic Institute for Interoperability Solutions (NIIS)
   Copyright (c) 2018 Estonian Information System Authority (RIA),
   Nordic Institute for Interoperability Solutions (NIIS), Population Register Centre (VRK)
   Copyright (c) 2015-2017 Estonian Information System Authority (RIA), Population Register Centre (VRK)

   Permission is hereby granted, free of charge, to any person obtaining a copy
   of this software and associated documentation files (the "Software"), to deal
   in the Software without restriction, including without limitation the rights
   to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
   copies of the Software, and to permit persons to whom the Software is
   furnished to do so, subject to the following conditions:

   The above copyright notice and this permission notice shall be included in
   all copies or substantial portions of the Software.

   THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
   IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
   FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
   AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
   LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
   OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
   THE SOFTWARE.
 -->
<template>
  <div>
    <ValidationObserver ref="form1" v-slot="{ invalid }">
      <div class="row-wrap">
        <div class="label">
          {{ $t('csr.usage') }}
          <helpIcon :text="$t('csr.helpUsage')" />
        </div>
        <div class="readonly-info-field">{{ usage }}</div>
      </div>

      <div class="row-wrap">
        <div class="label">
          {{ $t('csr.client') }}
          <helpIcon :text="$t('csr.helpClient')" />
        </div>
        <div class="readonly-info-field">{{ selectedMemberId }}</div>
      </div>

      <div class="row-wrap">
        <div class="label">
          {{ $t('csr.certificationService') }}
          <helpIcon :text="$t('csr.helpCertificationService')" />
        </div>

        <ValidationProvider name="csr.certService" rules="required" v-slot="{}">
          <v-select
            :items="filteredServiceList"
            item-text="name"
            item-value="name"
            class="form-input"
            v-model="certificationService"
            data-test="csr-certification-service-select"
          ></v-select>
        </ValidationProvider>
      </div>

      <div class="row-wrap">
        <div class="label">
          {{ $t('csr.csrFormat') }}
          <helpIcon :text="$t('csr.helpCsrFormat')" />
        </div>

        <ValidationProvider name="csr.csrFormat" rules="required" v-slot="{}">
          <v-select
            :items="csrFormatList"
            class="form-input"
            v-model="csrFormat"
            data-test="csr-format-select"
          ></v-select>
        </ValidationProvider>
      </div>

      <div class="button-footer">
        <large-button outlined @click="cancel" data-test="cancel-button">{{
          $t('action.cancel')
        }}</large-button>

        <div>
          <large-button
            v-if="showPreviousButton"
            @click="previous"
            outlined
            class="previous-button"
            data-test="previous-button"
            >{{ $t('action.previous') }}</large-button
          >
          <large-button
            :disabled="invalid"
            @click="done"
            data-test="save-button"
            >{{ $t(saveButtonText) }}</large-button
          >
        </div>
      </div>
    </ValidationObserver>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';
import { mapGetters } from 'vuex';
import { ValidationProvider, ValidationObserver } from 'vee-validate';
import HelpIcon from '@/components/ui/HelpIcon.vue';
import LargeButton from '@/components/ui/LargeButton.vue';
import { CsrFormat } from '@/openapi-types';

export default Vue.extend({
  components: {
    HelpIcon,
    LargeButton,
    ValidationObserver,
    ValidationProvider,
  },
  props: {
    saveButtonText: {
      type: String,
      default: 'action.continue',
    },
    showPreviousButton: {
      type: Boolean,
      default: true,
    },
  },
  data() {
    return {
      csrFormatList: Object.values(CsrFormat) as string[],
    };
  },
  computed: {
    ...mapGetters([
      'filteredServiceList',
      'isUsageReadOnly',
      'selectedMemberId',
      'usage',
    ]),

    csrFormat: {
      get(): string {
        return this.$store.getters.csrFormat;
      },
      set(value: string) {
        this.$store.commit('storeCsrFormat', value);
      },
    },
    certificationService: {
      get(): string {
        return this.$store.getters.certificationService;
      },
      set(value: string) {
        this.$store.commit('storeCertificationService', value);
      },
    },
  },
  methods: {
    done(): void {
      this.$emit('done');
    },
    previous(): void {
      this.$emit('previous');
    },
    cancel(): void {
      this.$emit('cancel');
    },
  },

  watch: {
    filteredServiceList(val) {
      // Set first certification service selected as default when the list is updated
      if (val?.length === 1) {
        this.certificationService = val[0].name;
      }
    },
  },
});
</script>

<style lang="scss" scoped>
@import '../../assets/wizards';

.readonly-info-field {
  max-width: 300px;
  height: 60px;
  padding-top: 12px;
}
</style>
