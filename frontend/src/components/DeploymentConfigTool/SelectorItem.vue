<template>
  <!-- eslint-disable vue/no-mutating-props -->
  <div class="selector-item" :class="{ editable }">
    <LabelSelect
      v-model:value="selector.key"
      :options="keys"
      :disabled="!editable"
      :modifier="labelKeyModifier"
      :capitalize="false"
      class="select key"
    />
    <LabelSelect
      v-model:value="selector.operator"
      :options="OPERATORS"
      :disabled="!editable"
      :modifier="operatorToText"
      class="select operator"
    />
    <LabelSelect
      v-if="selector.operator == OperatorType.OPERATOR_TYPE_IN"
      v-model:value="selector.values"
      :options="values"
      :disabled="!editable"
      :multiple="allowMultipleValues"
      :placeholder="$t('label.placeholder.select-values')"
      class="select values"
    />
    <div v-if="editable" class="remove" @click="$emit('remove')">
      <heroicons-outline:x class="w-4 h-4 text-control" />
    </div>
  </div>
</template>

<script lang="ts" setup>
/* eslint-disable vue/no-mutating-props */
import { computed, PropType, watch } from "vue";
import { ComposedDatabase } from "@/types";
import {
  LabelSelectorRequirement,
  OperatorType,
} from "@/types/proto/v1/project_service";
import {
  displayDeploymentMatchSelectorKey,
  getAvailableDeploymentConfigMatchSelectorKeyList,
  getLabelValuesFromDatabaseV1List,
} from "@/utils";
import LabelSelect from "./LabelSelect.vue";

const OPERATORS: OperatorType[] = [
  OperatorType.OPERATOR_TYPE_IN,
  OperatorType.OPERATOR_TYPE_EXISTS,
];

const props = defineProps({
  selector: {
    type: Object as PropType<LabelSelectorRequirement>,
    required: true,
  },
  databaseList: {
    type: Array as PropType<ComposedDatabase[]>,
    default: () => [],
  },
  editable: {
    type: Boolean,
    default: false,
  },
});

defineEmits<{
  (event: "remove"): void;
}>();

const keys = computed(() => {
  return getAvailableDeploymentConfigMatchSelectorKeyList(
    props.databaseList,
    true /* withVirtualLabelKeys */,
    true /* sort */
  );
});
const allowMultipleValues = computed(() => {
  return props.selector.key !== "environment";
});
const values = computed(() => {
  if (!props.selector.key) return [];
  return getLabelValuesFromDatabaseV1List(
    props.selector.key,
    props.databaseList,
    false /* !withEmptyValue */
  );
});

const resetValues = () => {
  props.selector.values = [];
};

const labelKeyModifier = (key: string | number) => {
  return displayDeploymentMatchSelectorKey(key as string);
};

const operatorToText = (op: string | number) => {
  if (op === OperatorType.OPERATOR_TYPE_IN) return "in";
  if (op === OperatorType.OPERATOR_TYPE_EXISTS) return "exists";
  return "";
};

watch(() => props.selector.key, resetValues);
watch(() => props.selector.operator, resetValues);
</script>

<style scoped lang="postcss">
.selector-item {
  @apply relative max-w-full flex shadow-sm rounded-md overflow-hidden;
}
.selector-item > * {
  @apply text-sm select-none text-main bg-white border border-control-border rounded-md cursor-default relative z-0;
}
.selector-item.editable > *:hover {
  @apply z-10 bg-control-bg-hover;
}
.selector-item > :not(:first-child, :last-child) {
  @apply rounded-l-none rounded-r-none;
}
.selector-item > :first-child {
  @apply rounded-r-none;
}
.selector-item > :last-child {
  @apply rounded-l-none;
}
.selector-item > :not(:first-child) {
  @apply -ml-px;
}
.select {
  @apply flex items-center relative;
}
.remove {
  @apply flex items-center pl-2 pr-2;
}
</style>
