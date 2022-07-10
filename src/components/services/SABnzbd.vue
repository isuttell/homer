<template>
  <Generic :item="item">
    <template #indicator>
      <div class="sabnzbd">
        <strong v-if="status" class="sabnzbd-property status" title="Status">
          {{ status }}
        </strong>
        <strong
          v-if="timeleft && timeleft !== '0:00:00'"
          class="sabnzbd-property timeleft"
          title="Speed"
        >
          {{ timeleft }} left
        </strong>
        <strong
          v-if="serverError"
          class="errors"
          title="Connection error to SABnzbd API, check url and apikey in config.yml"
        >
          ?
        </strong>
      </div>
    </template>
  </Generic>
</template>

<script>
import service from "@/mixins/service.js";
import Generic from "./Generic.vue";

export default {
  name: "SABnzbd",
  mixins: [service],
  props: {
    item: Object,
  },
  components: {
    Generic,
  },
  data: () => {
    return {
      status: null,
      timeleft: null,
      serverError: false,
    };
  },
  created: function () {
    this.fetchConfig();
  },
  methods: {
    fetchConfig: function () {
      this.fetch(`/api?mode=queue&apikey=${this.item.apikey}&output=json`)
        .then((body) => {
          ["status", "timeleft"].forEach((key) => {
            this[key] = body.queue[key];
          });
        })
        .catch((e) => {
          console.error(e);
          this.serverError = true;
        });
    },
  },
};
</script>

<style scoped lang="scss">
.sabnzbd {
  font-size: 0.8rem;
  color: white;
  display: flex;
  flex-direction: column;
  text-align: right;

  .sabnzbd-property {
    white-space: nowrap;
  }
}
</style>
