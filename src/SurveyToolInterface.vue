<template>
    <v-dialog v-model="questionTypeModal" @esc="toggleQuestionTypeModal()">
        <v-card>
            <v-card-text>
                <div class="form-grid">
                    <div class="field full">
                        <v-divider large :inlineTitle="false">
                            <span class="wrapper">
                                <span>Question type</span>
                            </span>
                        </v-divider>
                    </div>

                    <div class="field full">
                        <v-radio
                            v-for="type in questionTypes"
                            :key="type.value"
                            v-model="questionType"
                            :value="type.value"
                            block="true"
                            :label="type.text"
                        />
                    </div>
                </div>
            </v-card-text>

            <v-card-actions>
                <v-button @click="toggleQuestionTypeModal()" secondary>Cancel</v-button>

                <v-button @click="openQuestionInputModal()" :disabled="!questionType">Next</v-button>
            </v-card-actions>
        </v-card>
    </v-dialog>

    <v-dialog v-model="questionInputModal" @esc="closeQuestionInputModal">
        <component
            :is="questionType"
            @addQuestion="handleNewQuestion($event)"
            @closeModal="closeQuestionInputModal()"
            @goBack="goBackToQuestionTypeModal()"
            @saveQuestionChanges="saveQuestionChanges($event)"
            :questionToEdit="questionToEdit"
            :languages="languages"
        ></component>
    </v-dialog>

    <v-divider large :inlineTitle="false" class="question-title-margin">
        <span class="wrapper">
            <span>Questions</span>
        </span>
    </v-divider>

    <div v-if="Array.isArray(value) && value.length > 0">
        <v-notice v-if="duplicateQuestion" class="duplicate-question-notice" type="danger">
            Question already exists
        </v-notice>

        <v-list class="draggable-list">
            <div class="root-drag-container">
                <draggable @update:model-value="$emit('input', $event)" :model-value="value" handle=".drag-handle">
                    <template #item="{ element, index }">
                        <QuestionBlock
                            @deleteQuestion="deleteQuestion($event)"
                            @editQuestion="handleEditQuestion($event)"
                            :element="element"
                            :index="index"
                        ></QuestionBlock>
                    </template>
                </draggable>
            </div>
        </v-list>
    </div>

    <div v-else>
        <v-notice class="no-questions-notice"> There are no questions </v-notice>
    </div>

    <v-button @click="toggleQuestionTypeModal()" full-width>Add Question</v-button>
</template>

<script>
import { ref } from 'vue'
import Draggable from 'vuedraggable'

import FreetextQuestion from './components/input-question-components/FreetextQuestion.vue'
import MoodQuestion from './components/input-question-components/MoodQuestion.vue'
import NPSQuestion from './components/input-question-components/NPSQuestion.vue'
import SelectQuestion from './components/input-question-components/select-question/SelectQuestion.vue'
import QuestionBlock from './components/QuestionBlock.vue'

export default {
    props: {
        value: {
            type: Array,
            default: null,
        },
    },
    emits: ['input'],
    setup(props, { emit }) {
        const questionInputModal = ref(false)
        const questionTypeModal = ref(false)
        const questionType = ref(null)
        const questionToEdit = ref(null)
        const duplicateQuestion = ref(false)

        return {
            questionInputModal,
            questionTypeModal,
            questionType,
            questionToEdit,
            duplicateQuestion,
            toggleQuestionTypeModal,
            openQuestionInputModal,
            closeQuestionInputModal,
            goBackToQuestionTypeModal,
            questionExists,
            handleNewQuestion,
            deleteQuestion,
            handleEditQuestion,
            saveQuestionChanges,
        }

        function toggleQuestionTypeModal() {
            questionTypeModal.value = !questionTypeModal.value

            questionType.value = null

            duplicateQuestion.value = false
        }

        function openQuestionInputModal() {
            questionInputModal.value = true

            questionTypeModal.value = !questionTypeModal.value
        }

        function closeQuestionInputModal() {
            questionInputModal.value = false

            questionType.value = null

            questionToEdit.value = null
        }

        function goBackToQuestionTypeModal() {
            questionInputModal.value = false

            toggleQuestionTypeModal()
        }

        function questionExists(newQuestion) {
            return props.value.some(question => question.questionValue === newQuestion.questionValue)
        }

        function handleNewQuestion(newQuestion) {
            if (Array.isArray(props.value)) {
                if (questionExists(newQuestion)) {
                    duplicateQuestion.value = true
                } else {
                    emitValue([...props.value, newQuestion])
                }
            } else {
                if (props.value != null) {
                    console.warn('The repeater interface expects an array as value, but the given value is no array.')
                }

                emitValue([newQuestion])
            }

            closeQuestionInputModal()
        }

        function deleteQuestion(questionToDelete) {
            emitValue(props.value.filter(question => question.questionValue !== questionToDelete.questionValue))
        }

        function handleEditQuestion(editQuestion) {
            questionToEdit.value = {
                ...props.value[editQuestion.index],
                index: editQuestion.index,
            }

            questionType.value = editQuestion.type

            questionInputModal.value = true

            duplicateQuestion.value = false
        }

        function saveQuestionChanges(newQuestion) {
            emitValue(props.value.map((question, index) => (index === newQuestion.index ? newQuestion : question)))

            closeQuestionInputModal()
        }

        function emitValue(value) {
            if (!value) {
                return emit('input', null)
            }

            return emit('input', value)
        }
    },
    data() {
        return {
            questionTypes: [
                {
                    text: 'Freetext Question',
                    value: 'FreetextQuestion',
                },
                {
                    text: 'Select Question',
                    value: 'SelectQuestion',
                },
                {
                    text: 'NPS Question',
                    value: 'NPSQuestion',
                },
                {
                    text: 'Mood Question',
                    value: 'MoodQuestion',
                },
            ],
            languages: [
                {
                    value: 'EN',
                    text: 'EN',
                },
                {
                    value: 'PL',
                    text: 'PL',
                },
                {
                    value: 'RO',
                    text: 'RO',
                },
            ],
        }
    },

    components: {
        QuestionBlock,
        FreetextQuestion,
        SelectQuestion,
        NPSQuestion,
        MoodQuestion,
        Draggable,
    },
    computed: {},
    methods: {},
}
</script>

<style lang="scss">
.no-questions-notice {
    margin: 28px 0;
}

.v-sheet {
    --v-sheet-width: 600px;
    --v-sheet-background-color: var(--background-page);
}

.v-card {
    --v-card-min-width: 600px !important;
    --form-vertical-gap: 40px !important;
    --v-card-background-color: var(--background-page);

    .v-card-text {
        padding-top: 24px;
        margin-top: 4px;
    }
}

.v-divider {
    --v-divider-color: var(--border-subdued);
}

.question-title-margin {
    margin-top: 40px !important;
}

.back-button {
    margin-right: 16px;
}

.header-wrapper {
    display: flex;
    align-items: end;
}

.draggable-list {
    .sortable-ghost {
        border-radius: var(--border-radius);
        border: 2px dashed var(--primary);

        & > * {
            opacity: 0;
        }
    }
}

.root-drag-container {
    margin-top: 20px;
    padding: 8px 0;
    overflow: hidden;
}

.drag-handle {
    cursor: grab;
}

.v-radio {
    margin-bottom: 8px;
}

.duplicate-question-notice {
    margin-top: 20px;
}

.v-select {
    display: flex !important;
    justify-content: end;

    .v-input {
        width: max-content !important;
        height: 40px;
    }
}
</style>
