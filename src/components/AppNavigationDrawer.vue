<template>
  <v-navigation-drawer
      app
      clipped
      class="safe-area-ios"
      v-model="drawer"
    >
    <v-list dense class="d-block d-sm-none">
      <v-list-item inactive>
        <v-text-field
          solo-inverted
          flat
          hide-details
          single-line
          label="Search"
          v-model="searchQuery"
          clearable
        />
      </v-list-item>
    </v-list>

    <v-list dense>
      <v-subheader>CHANNELS</v-subheader>
      <v-list-item-group v-model="selectedChannel" color="primary">
        <v-list-item>
          <v-list-item-content>
            <v-list-item-title>All channels</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
        <v-list-item v-for="(channel, index) in channels" :key="channel.id">
          <v-list-item-content>
            <v-list-item-title v-text="channel.name"></v-list-item-title>
          </v-list-item-content>
          <v-list-item-action>
            <v-btn icon @click="removeChannel(index)">
              <v-icon>mdi-close</v-icon>
            </v-btn>
          </v-list-item-action>
        </v-list-item>
      </v-list-item-group>
    </v-list>
    <template v-slot:append>
      <v-divider/>
      <v-list dense>
        <SettingsDialog/>
        <AboutDialog/>
      </v-list>
    </template>
  </v-navigation-drawer>
</template>

<script>
import { mapActions, mapState } from 'vuex';

import AboutDialog from './AboutDialog.vue';
import SettingsDialog from './SettingsDialog.vue';

export default {
  name: 'AppNavigationDrawer',
  components: {
    AboutDialog,
    SettingsDialog,
  },
  computed: {
    drawer: {
      get() {
        return this.$store.state.drawer;
      },
      set(value) {
        if (this.$store.state.drawer !== value) {
          this.$store.dispatch('toggleDrawer');
        }
      },
    },
    searchQuery: {
      get() {
        return this.$store.state.searchQuery;
      },
      set(value) {
        this.$store.dispatch('search', value);
      },
    },
    selectedChannel: {
      get() {
        return this.$store.state.selectedChannel;
      },
      set(value) {
        this.$store.dispatch('selectChannel', value);
      },
    },
    ...mapState([
      'channels',
    ]),
  },
  methods: mapActions([
    'removeChannel',
  ]),
  watch: {
    selectedChannel(value, oldValue) {
      if (value === undefined) {
        this.selectedChannel = oldValue - 1;
      } else if (value < 0 || oldValue < 0) {
        this.selectedChannel = 0;
      } else {
        const channelIndex = value - 1;

        if (this.channels[channelIndex] === undefined) {
          this.selectedChannel = value - 1;
        }
      }
    },
  },
};
</script>
