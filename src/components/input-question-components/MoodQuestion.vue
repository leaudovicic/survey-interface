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
                                <span>Mood Question</span>
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

                <div class="field full">
                    <div class="label type-label">Thumb down</div>
                    <v-input v-model="question.options[0].text" trim>
                        <template #prepend>
                            <v-icon name="thumb_down" />
                        </template>
                    </v-input>
                </div>

                <div class="field full">
                    <div class="label type-label">Neutral mood</div>
                    <v-input v-model="question.options[1].text" trim>
                        <template #prepend>
                            <v-icon name="sentiment_neutral" />
                        </template>
                    </v-input>
                </div>

                <div class="field full">
                    <div class="label type-label">Thumb down</div>
                    <v-input v-model="question.options[2].text" trim>
                        <template #prepend>
                            <v-icon name="thumb_up" />
                        </template>
                    </v-input>
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
    name: 'MoodQuestion',
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
                type: 'MoodQuestion',
                questionValue: null,
                options: [
                    {
                        key: 1,
                        icon: 'thumbs-down',
                        text: null,
                    },
                    {
                        key: 2,
                        icon: 'neutral',
                        text: null,
                    },
                    {
                        key: 3,
                        icon: 'thumbs-up',
                        text: null,
                    },
                ],
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
            return !(this.question.questionValue && this.question.options.every(option => option.text))
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
        },

        goBack() {
            this.$emit('goBack')
        },
    },
}
</script>

<style scoped></style>
