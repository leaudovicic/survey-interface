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
                                <span>NPS question</span>
                            </span>
                        </v-divider>
                    </div>
                </div>

                <div class="field full">
                    <div class="field full">
                        <v-select v-model="language" :items="languages" />
                    </div>

                    <div class="label type-label">Question</div>

                    <v-textarea v-model="question.questionValue" autofocus trim />
                </div>

                <div class="field half">
                    <div class="label type-label">Min value</div>
                    <v-input v-model="question.range.min.value" type="number" trim> </v-input>
                </div>

                <div class="field half-right">
                    <div class="label type-label">Min value label</div>
                    <v-input v-model="question.range.min.text" type="text" trim> </v-input>
                </div>

                <div class="field half">
                    <div class="label type-label">Max value</div>
                    <v-input v-model="question.range.max.value" type="number" trim> </v-input>
                </div>

                <div class="field half-right">
                    <div class="label type-label">Max value label</div>
                    <v-input v-model="question.range.max.text" type="text" trim> </v-input>
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
export default {
    name: 'NPS',
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
            language: 'EN',
            question: {
                type: 'NPSQuestion',
                questionValue: null,
                range: {
                    min: {
                        value: null,
                        text: null,
                    },
                    max: {
                        value: null,
                        text: null,
                    },
                },
            },
        }
    },
    created() {
        if (this.questionToEdit) {
            this.question = JSON.parse(JSON.stringify(this.questionToEdit))
        }
    },
    computed: {
        questionSubmitDisabled() {
            return !(
                this.question.questionValue &&
                this.question.range.min.value !== null &&
                this.question.range.max.value
            )
        },

        buttonName() {
            if (this.questionToEdit) {
                return 'Save'
            }

            return 'Add'
        },
    },
    methods: {
        submitQuestion() {
            if (this.questionToEdit) {
                this.$emit('saveQuestionChanges', this.question)
            } else {
                this.$emit('addQuestion', this.question)
            }
        },

        closeModal() {
            this.$emit('closeModal')
        },

        saveQuestionChanges() {
            this.$emit('saveQuestionChanges', this.question)

            console.log('saveChanges')
        },

        goBack() {
            this.$emit('goBack')
        },
    },
}
</script>

<style scoped></style>
