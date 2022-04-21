<template>
    <v-card>
        <v-card-text>
            <div class="form-grid">
                <div class="field full">
                    <div class="header-wrapper">
                        <v-button v-if="!questionToEdit" @click="goBack" rounded icon secondary class="back-button">
                            <v-icon name="arrow_back"></v-icon>
                        </v-button>

                        <v-divider large :inlineTitle="false">
                            <span class="wrapper">
                                <span>Select question</span>
                            </span>
                        </v-divider>
                    </div>
                </div>

                <div class="field full first-field-container">
                    <div class="switch-container">
                        <v-switch v-model="question.multi" />

                        <span class="label type-label" :class="{ 'multi-select': question.multi }">Multi-select</span>
                    </div>

                    <div class="field full">
                        <v-select v-model="language" :items="languages" />
                    </div>
                </div>

                <div class="field full">
                    <div class="label type-label">Question</div>

                    <v-textarea v-model="question.questionValue" autofocus trim />
                </div>

                <div class="field full">
                    <div class="label type-label">Add Select options</div>

                    <div class="select-option-wrapper">
                        <v-input v-model="newOption" @keydown.enter="addNewOption" trim>
                            <template #append>
                                <v-icon @click="addNewOption" name="add" :disabled="!newOption" role="button"></v-icon>
                            </template>
                        </v-input>
                    </div>

                    <v-notice v-if="duplicateOption" type="danger">Option already exists</v-notice>
                </div>

                <div class="field full">
                    <div v-if="question.options.length" class="label type-label">Options</div>

                    <div class="draggable-list">
                        <div class="field-grid">
                            <draggable v-model="question.options" handle=".drag-handle">
                                <template #item="{ element, index }">
                                    <SelectQuestionOption
                                        @changeOption="updateOption"
                                        @deleteOption="deleteOption"
                                        :option="element"
                                        :index="index"
                                    />
                                </template>
                            </draggable>
                        </div>
                    </div>
                </div>
            </div>
        </v-card-text>

        <v-card-actions>
            <v-button @click="closeModal" secondary>Cancel</v-button>

            <v-button @click="submitQuestion" :disabled="questionSubmitDisabled">{{ buttonName }}</v-button>
        </v-card-actions>
    </v-card>
</template>

<script>
import Draggable from 'vuedraggable'

import SelectQuestionOption from './SelectQuestionOption.vue'

export default {
    name: 'SelectQuestion',
    props: {
        questionToEdit: {
            type: Object,
            default: null,
        },
        languages: {
            type: Array,
            default: null,
        },
    },
    emits: ['addQuestion', 'closeModal', 'saveQuestionChanges', 'goBack'],
    data() {
        return {
            newOption: null,
            duplicateOption: false,
            language: 'EN',
            question: {
                type: 'SelectQuestion',
                questionValue: null,
                multi: false,
                options: [],
            },
        }
    },
    created() {
        if (this.questionToEdit) {
            this.question = JSON.parse(JSON.stringify(this.questionToEdit))
        }
    },
    components: { SelectQuestionOption, Draggable },
    computed: {
        questionSubmitDisabled() {
            return !(this.question.questionValue && this.question.options.length)
        },

        buttonName() {
            if (this.questionToEdit) {
                return 'Save'
            }

            return 'Add'
        },
    },
    methods: {
        closeModal() {
            this.$emit('closeModal')
        },

        addNewOption() {
            if (this.newOption && !this.duplicateOption) {
                this.question.options.push(this.newOption)

                this.newOption = null
            }
        },

        optionExists(option) {
            return this.question.options.includes(option)
        },

        updateOption(optionToUpdate) {
            if (this.optionExists(optionToUpdate.value)) {
                optionToUpdate.value = null
            }

            this.question.options = this.question.options.map((option, index) =>
                index === optionToUpdate.index ? optionToUpdate.value : option
            )

            if (!optionToUpdate.value) {
                this.deleteOption(optionToUpdate)
            }
        },

        deleteOption(optionToDelete) {
            this.question.options = this.question.options.filter(option => option !== optionToDelete.value)
        },

        submitQuestion() {
            if (this.questionToEdit) {
                this.$emit('saveQuestionChanges', this.question)
            } else {
                this.$emit('addQuestion', this.question)
            }
        },

        goBack() {
            this.$emit('goBack')
        },
    },
    watch: {
        newOption(newValue) {
            this.duplicateOption = this.optionExists(newValue)
        },
    },
}
</script>

<style lang="scss" scoped>
.v-notice {
    margin-top: 10px;
}

.switch-container {
    display: flex;
    align-items: center;

    .type-label {
        margin: 0 8px;
        color: var(--border-normal);
    }

    .multi-select {
        color: var(--v-switch-color);
    }
}

.first-field-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
}
</style>
