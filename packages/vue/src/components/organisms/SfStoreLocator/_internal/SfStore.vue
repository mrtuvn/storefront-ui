<template>
  <div class="sf-store">
    <!-- @slot Use this slot to change distance element -->
    <slot name="distance">
      <div v-if="distance" class="sf-store__distance">
        <span>{{ distance }}km</span> away from you
      </div>
    </slot>
    <div class="sf-store__media" @click="centerOn(latlng)">
      <!-- @slot Use this slot to show media elements -->
      <slot name="media">
        <SfImage
          :src="picture"
          :alt="`${name} picture`"
          :width="82"
          :height="112"
        />
      </slot>
    </div>
    <div class="sf-store__info">
      <div class="sf-store__heading">
        <!-- @slot Use this slot to show heading -->
        <slot name="heading">
          <div class="sf-store__name">
            {{ name }}
          </div>
        </slot>
      </div>
      <!-- @slot This is the default slot of the component, placed on the right of the picture -->
      <slot>
        <div v-if="address" class="sf-store__address">
          {{ address }}
        </div>
        <SfCharacteristic
          v-if="phone"
          icon="phone"
          size-icon="16px"
          class="sf-store__property"
        >
          <template #text>
            <a
              v-focus
              :href="`tel:${phone}`"
              tabindex="0"
              class="sf-store__property-link"
              >{{ phone }}</a
            >
          </template>
        </SfCharacteristic>
        <SfCharacteristic
          v-if="email"
          icon="mail"
          size-icon="16px"
          class="sf-store__property"
        >
          <template #text>
            <a
              v-focus
              :href="`mailto:${email}`"
              tabindex="0"
              class="sf-store__property-link"
              >{{ email }}</a
            >
          </template>
        </SfCharacteristic>
      </slot>
    </div>
  </div>
</template>
<script>
import { focus } from "../../../../utilities/directives";
import SfImage from "../../../atoms/SfImage/SfImage.vue";
import SfCharacteristic from "../../../molecules/SfCharacteristic/SfCharacteristic.vue";
export default {
  name: "SfStore",
  inject: [
    "registerStore",
    "removeStore",
    "centerOn",
    "locatorData",
    "getGeoDistance",
  ],
  components: {
    SfImage,
    SfCharacteristic,
  },
  directives: {
    focus: focus,
  },
  props: {
    /**
     * Name of the store
     */
    latlng: {
      type: Array,
      default: () => [],
    },
    /**
     * Name of the store
     */
    name: {
      type: String,
      default: "",
    },
    /**
     * Url of the picture of the store
     */
    picture: {
      type: [String, Object],
      default: null,
    },
    /**
     * Address of the store
     */
    address: {
      type: String,
      default: "",
    },
    /**
     * Phone number of the store
     */
    phone: {
      type: String,
      default: null,
    },
    /**
     * Email of the store
     */
    email: {
      type: String,
      default: null,
    },
  },
  computed: {
    distance() {
      if (this.locatorData && this.locatorData.userPosition) {
        const dictLatLng = {
          lat: this.latlng[0],
          lng: this.latlng[1],
        };
        return this.getGeoDistance(this.locatorData.userPosition, dictLatLng);
      }
      return null;
    },
  },
  created() {
    if (this.registerStore) {
      this.registerStore(this.$props);
    }
  },
  beforeDestroy() {
    if (this.removeStore) {
      this.removeStore(this.$props);
    }
  },
};
</script>
