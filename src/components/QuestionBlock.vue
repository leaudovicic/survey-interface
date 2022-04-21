<template>
    <div @dblclick="editQuestion" class="collection-item" data-draggable="true">
        <v-list-item dense clickable block>
            <v-list-item-icon>
                <v-icon name="drag_indicator" class="drag-handle" left />
            </v-list-item-icon>

            <div class="collection-name">
                {{ index + 1 }}. {{ element.questionValue }} <span class="question-type">{{ questionType }}</span>
            </div>

            <v-menu show-arrow placement="left">
                <template #activator="{ toggle }">
                    <v-icon @click="toggle" name="more_vert" right role="button"></v-icon>
                </template>

                <v-list>
                    <v-list-item @click="editQuestion" clickable>
                        <v-list-item-icon>
                            <v-icon name="edit"></v-icon>
                        </v-list-item-icon>

                        <v-list-item-content>Edit question</v-list-item-content>
                    </v-list-item>

                    <v-list-item @click="deleteQuestion" class="danger" clickable>
                        <v-list-item-icon>
                            <v-icon name="delete"></v-icon>
                        </v-list-item-icon>

                        <v-list-item-content>Delete question</v-list-item-content>
                    </v-list-item>
                </v-list>
            </v-menu>
        </v-list-item>
    </div>
</template>

<script>
export default {
    name: 'QuestionBlock',
    props: {
        element: {
            type: Object,
            default: null,
        },
        index: {
            type: Number,
            default: null,
        },
    },
    emits: ['deleteQuestion', 'editQuestion'],
    computed: {
        questionType() {
            switch (this.element.type) {
                case 'FreetextQuestion':
                    return 'Freetext'
                    break

                case 'SelectQuestion':
                    return 'Select'
                    break

                case 'NPSQuestion':
                    return 'NPS'
                    break

                case 'MoodQuestion':
                    return 'Mood'
                    break

                default:
                    return ''
            }
        },
        questionToEdit() {
            return { ...this.element, index: this.index }
        },
    },
    methods: {
        deleteQuestion() {
            this.$emit('deleteQuestion', this.element)
        },
        editQuestion() {
            this.$emit('editQuestion', this.questionToEdit)
        },
    },
}
</script>

<style lang="scss" scoped>
.danger {
    --v-list-item-color: var(--danger);
    --v-list-item-color-hover: var(--danger);
    --v-list-item-icon-color: var(--danger);
}

.collection-item {
    margin-bottom: 8px;
}

.collection-name {
    display: flex;
    flex-grow: 1;
    align-items: center;
    height: 100%;
    font-family: var(--family-monospace);
    pointer-events: none;
}

.question-type {
    margin-left: 15px;
    font-size: 12px;
    color: var(--foreground-subdued);
}
</style>
