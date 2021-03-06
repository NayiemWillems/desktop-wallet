<template>
  <div
    v-click-outside.capture="emitClose"
    :class="isSidebarExpanded ? 'WalletSidebarFilters--expanded' : 'WalletSidebarFilters--collapsed'"
    class="WalletSidebarFilters absolute z-20 rounded-lg theme-light"
  >
    <div
      class="bg-theme-feature p-10 rounded-lg shadow font-bold"
    >
      <WalletSidebarFiltersSearchInput
        v-model="filters.searchQuery"
        :placeholder="hasContacts
          ? $t('WALLET_SIDEBAR.SEARCH.PLACEHOLDER_CONTACTS')
          : $t('WALLET_SIDEBAR.SEARCH.PLACEHOLDER_WALLETS')
        "
        @input="setSearchQuery"
      />
    </div>

    <MenuOptions class="WalletSidebarFilters__sorting mt-2 theme-light rounded-lg shadow font-bold">
      <div class="mx-10 py-5 mb-2 text-theme-settings-heading select-none">
        {{ $t('WALLET_SIDEBAR.SORT.BY') }}
      </div>
      <MenuOptionsItem
        :title="$t('WALLET_SIDEBAR.SORT.NAME_ASC')"
        :class="sortOrder === 'name-asc' ? 'WalletSidebarFilters__sorting__order--selected' : ''"
        @click="setSort('name-asc')"
      />
      <MenuOptionsItem
        :title="$t('WALLET_SIDEBAR.SORT.NAME_DESC')"
        :class="sortOrder === 'name-desc' ? 'WalletSidebarFilters__sorting__order--selected' : ''"
        @click="setSort('name-desc')"
      />
      <MenuOptionsItem
        :title="$t('WALLET_SIDEBAR.SORT.BALANCE_ASC')"
        :class="sortOrder === 'balance-asc' ? 'WalletSidebarFilters__sorting__order--selected' : ''"
        @click="setSort('balance-asc')"
      />
      <MenuOptionsItem
        :title="$t('WALLET_SIDEBAR.SORT.BALANCE_DESC')"
        :class="sortOrder === 'balance-desc' ? 'WalletSidebarFilters__sorting__order--selected' : ''"
        @click="setSort('balance-desc')"
      />
    </MenuOptions>

    <MenuOptions class="WalletSidebarFilters__settings mt-2 rounded-lg shadow font-medium">
      <MenuOptionsItem
        :title="hasContacts
          ? $t('WALLET_SIDEBAR.FILTERS.HIDE_EMPTY_CONTACTS')
          : $t('WALLET_SIDEBAR.FILTERS.HIDE_EMPTY_WALLETS')
        "
        @click="toggleSelect('hide-empty')"
      >
        <div
          slot="controls"
          class="pointer-events-none"
        >
          <ButtonSwitch
            ref="hide-empty"
            :is-active="hideEmpty"
            class="theme-light"
            background-color="var(--theme-settings-switch)"
            @change="setHideEmpty"
          />
        </div>
      </MenuOptionsItem>
      <MenuOptionsItem
        v-if="hasLedger && !hasContacts"
        :title="$t('WALLET_SIDEBAR.FILTERS.HIDE_LEDGER')"
        @click="toggleSelect('hide-ledger')"
      >
        <div
          slot="controls"
          class="pointer-events-none"
        >
          <ButtonSwitch
            ref="hide-ledger"
            :is-active="hideLedger"
            class="theme-light"
            background-color="var(--theme-settings-switch)"
            @change="setHideLedger"
          />
        </div>
      </MenuOptionsItem>
    </MenuOptions>
  </div>
</template>

<script>
import { ButtonSwitch } from '@/components/Button'
import { MenuOptions, MenuOptionsItem } from '@/components/Menu'
import WalletSidebarFiltersSearchInput from './WalletSidebarFiltersSearchInput'

export default {
  name: 'WalletSidebarFilters',

  components: {
    ButtonSwitch,
    MenuOptions,
    MenuOptionsItem,
    WalletSidebarFiltersSearchInput
  },

  props: {
    hasContacts: {
      type: Boolean,
      required: false,
      default: false
    },
    // Show the Ledger options or not
    hasLedger: {
      type: Boolean,
      required: false,
      default: false
    },
    isSidebarExpanded: {
      type: Boolean,
      required: false,
      default: false
    },
    outsideClick: {
      type: Boolean,
      required: false,
      default: false
    },
    hideEmpty: {
      type: Boolean,
      required: false,
      default: false
    },
    // Hide Ledger wallets
    hideLedger: {
      type: Boolean,
      required: false,
      default: false
    },
    searchQuery: {
      type: String,
      required: false,
      default: ''
    },
    sortOrder: {
      type: String,
      required: false,
      default: 'name-asc'
    }
  },

  data () {
    return {
      filters: {
        hideEmpty: this.hideEmpty,
        hideLedger: this.hideLedger,
        searchQuery: this.searchQuery
      },
      sort: {
        order: this.sortOrder
      }
    }
  },

  watch: {
    hideEmpty (value) {
      this.filters.hideEmpty = value
    },

    hideLedger (value) {
      this.filters.hideLedger = value
    },

    searchQuery (value) {
      this.filters.searchQuery = value
    },

    order (value) {
      this.sort.order = value
    }
  },

  methods: {
    setSearchQuery (query) {
      this.filters.searchQuery = query
      this.emitFilter()
    },

    setHideEmpty (isHidden) {
      this.filters.hideEmpty = isHidden
      this.emitFilter()
    },

    setHideLedger (isHidden) {
      this.filters.hideLedger = isHidden
      this.emitFilter()
    },

    setSort (order) {
      this.sort.order = order
      this.emitSort()
    },

    toggleSelect (name) {
      this.$refs[name].toggle()
    },

    emitFilter () {
      this.$emit('filter', this.filters)
    },

    emitSort () {
      this.$emit('sort', this.sort.order)
    },

    emitClose (context) {
      this.$emit('close', context)
    }
  }
}
</script>

<style lang="postcss" scoped>
.WalletSidebarFilters {
  position: fixed;
  width: 380px;
  /* The expanded sidebar uses `.w-1/7` */
  right: calc(12.5% - 0.5rem);
  top: 0.75rem;
  transition: right 0.4s;
}

.WalletSidebarFilters--expanded {
  /* The expanded sidebar uses `.w-1/3` */
  right: calc(33.33333% - 2.5rem);
}

.WalletSidebarFilters__sorting .MenuOptionsItem {
  transition: color 0.2s;
}
.WalletSidebarFilters__sorting__order--selected {
  color: var(--theme-settings-heading) !important;
}

.WalletSidebarFilters .MenuOptions {
  @apply .rounded-lg
}
/* To not render the bubble arrow */
.WalletSidebarFilters .MenuOptions:after {
  border-width: 0px
}
</style>
