<template>
  <HoppSmartModal
    v-if="show"
    dialog
    :title="`${t('collection.edit')}`"
    @close="hideModal"
  >
    <template #body>
      <HoppSmartInput
        v-model="editingName"
        placeholder=" "
        :label="t('action.label')"
        input-styles="floating-input"
        @submit="saveCollection"
      />
    </template>
    <template #footer>
      <span class="flex space-x-2">
        <HoppButtonPrimary
          :label="`${t('action.save')}`"
          outline
          @click="saveCollection"
        />
        <HoppButtonSecondary
          :label="`${t('action.cancel')}`"
          outline
          filled
          @click="hideModal"
        />
      </span>
    </template>
  </HoppSmartModal>
</template>

<script setup lang="ts">
import { ref, watch } from "vue"
import { editGraphqlCollection } from "~/newstore/collections"
import { useToast } from "@composables/toast"
import { useI18n } from "@composables/i18n"
import { HoppCollection } from "@hoppscotch/data"

const props = defineProps<{
  show: boolean
  editingCollectionIndex: number | null
  editingCollection: HoppCollection | null
  editingCollectionName: string
}>()

const emit = defineEmits<{
  (e: "hide-modal"): void
}>()

const t = useI18n()
const toast = useToast()

const editingName = ref<string | null>()

watch(
  () => props.editingCollectionName,
  (val) => {
    editingName.value = val
  }
)

const saveCollection = () => {
  if (!editingName.value) {
    toast.error(`${t("collection.invalid_name")}`)
    return
  }

  // TODO: Better typechecking here ?
  const collectionUpdated = {
    ...(props.editingCollection as any),
    name: editingName.value,
  }

  editGraphqlCollection(props.editingCollectionIndex, collectionUpdated)
  hideModal()
}

const hideModal = () => {
  editingName.value = null
  emit("hide-modal")
}
</script>
