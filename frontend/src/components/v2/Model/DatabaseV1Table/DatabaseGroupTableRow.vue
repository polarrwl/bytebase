<template>
  <div v-if="showSelectionColumn" class="bb-grid-cell !px-2">
    <slot name="selection" :database="databaseGroup" />
  </div>
  <div class="bb-grid-cell">
    <div class="flex items-center space-x-2">
      <SQLEditorButtonV1 :disabled="true" :tooltip="true" />
      <span>{{ databaseGroup.databasePlaceholder }}</span>
      <BBBadge text="GROUP" :can-remove="false" class="text-xs" />
    </div>
  </div>
  <div class="bb-grid-cell">
    <!-- environment -->
    <EnvironmentV1Name
      :environment="databaseGroup.environment"
      :link="false"
      tag="div"
    />
  </div>
  <div class="bb-grid-cell">
    <!-- schema version -->
    <span>-</span>
  </div>
  <div class="bb-grid-cell">
    <!-- project -->
    <ProjectCol
      :project="databaseGroup.project"
      :mode="mode"
      :show-tenant-icon="showTenantIcon"
    />
  </div>
  <div class="bb-grid-cell">
    <!-- instance -->
    <span>-</span>
  </div>
  <div v-if="showLabelsColumn" class="bb-grid-cell">
    <!-- labels -->
    <span>-</span>
  </div>
</template>

<script lang="ts" setup>
import BBBadge from "@/bbkit/BBBadge.vue";
import { SQLEditorButtonV1 } from "@/components/DatabaseDetail";
import { EnvironmentV1Name } from "@/components/v2";
import { ComposedDatabaseGroup } from "@/types";
import ProjectCol from "./ProjectCol.vue";

defineProps<{
  databaseGroup: ComposedDatabaseGroup;
  mode: string;
  showSelectionColumn: boolean;
  showMiscColumn: boolean;
  showSchemaVersionColumn: boolean;
  showProjectColumn: boolean;
  showTenantIcon: boolean;
  showEnvironmentColumn: boolean;
  showInstanceColumn: boolean;
  showLabelsColumn: boolean;
  allowQuery: boolean;
}>();
</script>
