<template>
  <div class="app-page-toolbar">
    <router-link :to="{ path: '/loan/new' }" v-if="hasPermissionToCreate">
      <el-button type="primary">
        <app-i18n code="common.new"></app-i18n>
      </el-button>
    </router-link>

    <router-link :to="{ path: '/loan/import' }" v-if="hasPermissionToImport">
      <el-button type="primary">
        <app-i18n code="common.import"></app-i18n>
      </el-button>
    </router-link>

    <el-tooltip
      :content="destroyButtonTooltip"
      :disabled="!destroyButtonTooltip"
      v-if="hasPermissionToDestroy"
    >
      <span>
        <el-button
          :disabled="destroyButtonDisabled"
          @click="doDestroyAllSelected"

          type="primary"
        >
          <app-i18n code="common.destroy"></app-i18n>
        </el-button>
      </span>
    </el-tooltip>

    <router-link
      :to="{ path: '/audit-logs', query: { entityNames: 'loan' } }"
      v-if="hasPermissionToAuditLogs"
    >
      <el-button>
        <app-i18n code="auditLog.menu"></app-i18n>
      </el-button>
    </router-link>

    <el-tooltip :content="exportButtonTooltip" :disabled="!exportButtonTooltip">
      <span>
        <el-button
          :disabled="exportButtonDisabled"
          @click="doExport()"
          
        >
          <app-i18n code="common.export"></app-i18n>
        </el-button>
      </span>
    </el-tooltip>
  </div>
</template>

<script>
import { AuditLogPermissions } from '@/modules/audit-log/audit-log-permissions';
import { mapGetters, mapActions } from 'vuex';
import { LoanPermissions } from '@/modules/loan/loan-permissions';
import { i18n } from '@/i18n';

export default {
  name: 'app-loan-list-toolbar',

  computed: {
    ...mapGetters({
      currentUser: 'auth/currentUser',
      hasRows: 'loan/list/hasRows',
      loading: 'loan/list/loading',
      exportLoading: 'loan/list/exportLoading',
      selectedRows: 'loan/list/selectedRows',
      destroyLoading: 'loan/destroy/loading',
    }),

    hasPermissionToAuditLogs() {
      return new AuditLogPermissions(this.currentUser).read;
    },

    hasPermissionToCreate() {
      return new LoanPermissions(this.currentUser).create;
    },

    hasPermissionToEdit() {
      return new LoanPermissions(this.currentUser).edit;
    },

    hasPermissionToImport() {
      return new LoanPermissions(this.currentUser).import;
    },

    hasPermissionToDestroy() {
      return new LoanPermissions(this.currentUser)
        .destroy;
    },

    exportButtonDisabled() {
      return (
        !this.hasRows || this.loading || this.exportLoading
      );
    },

    exportButtonTooltip() {
      return !this.hasRows
        ? i18n('common.noDataToExport')
        : null;
    },

    removeButtonDisabled() {
      return !this.selectedRows?.length || this.loading;
    },

    removeButtonTooltip() {
      return !this.selectedRows?.length
        ? i18n('common.mustSelectARow')
        : null;
    },

    enableButtonDisabled() {
      return !this.selectedRows?.length || this.loading;
    },

    enableButtonTooltip() {
      return !this.selectedRows?.length
        ? i18n('common.mustSelectARow')
        : null;
    },

    disableButtonDisabled() {
      return !this.selectedRows?.length || this.loading;
    },

    disableButtonTooltip() {
      return !this.selectedRows?.length
        ? i18n('common.mustSelectARow')
        : null;
    },

    destroyButtonDisabled() {
      return (
        !this.selectedRows?.length ||
        this.loading ||
        this.destroyLoading
      );
    },

    destroyButtonTooltip() {
      if (!this.selectedRows?.length || this.loading) {
        return i18n('common.mustSelectARow');
      }

      return null;
    },
  },

  methods: {
    ...mapActions({
      doExport: 'loan/list/doExport',
      doRemoveAllSelected:
        'loan/list/doRemoveAllSelected',
      doDisableAllSelected:
        'loan/list/doDisableAllSelected',
      doEnableAllSelected:
        'loan/list/doEnableAllSelected',
      doDestroyAll: 'loan/destroy/doDestroyAll',
    }),

    async doDestroyAllSelected() {
      try {
        await this.$confirm(
          i18n('common.areYouSure'),
          i18n('common.confirm'),
          {
            confirmButtonText: i18n('common.yes'),
            cancelButtonText: i18n('common.no'),
            type: 'warning',
          },
        );

        return this.doDestroyAll(
          this.selectedRows.map((item) => item.id),
        );
      } catch (error) {
        // no
      }
    },
  },
};
</script>

<style>
</style>
