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
                                <span>Freetext question</span>
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
            </div>
        </v-card-text>

        <v-card-actions>
            <v-button @click="closeModal" secondary>Cancel</v-button>

            <v-button @click="submitQuestion" :disabled="!question.questionValue">
                {{ buttonName }}
            </v-button>
        </v-card-actions>
    </v-card>
</template>

<script>
export default {
    name: 'FreetextQuestion',
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
    emits: ['addQuestion', 'closeModal', 'goBack', 'saveQuestionChanges'],
    data() {
        return {
            language: 'EN',
            question: {
                type: 'FreetextQuestion',
                questionValue: null,
            },
        }
    },
    created() {
        if (this.questionToEdit) {
            this.question = JSON.parse(JSON.stringify(this.questionToEdit))
        }
    },
    computed: {
        buttonName() {
            if (this.questionToEdit) {
                return 'Save'
            }

            return 'Add'
        },
    },
    methods: {
        submitQuestion() {
            if (this.question.questionValue) {
                if (this.questionToEdit) {
                    this.$emit('saveQuestionChanges', this.question)
                } else {
                    this.$emit('addQuestion', this.question)
                }
            }
        },

        closeModal() {
            this.$emit('closeModal')
        },

        goBack() {
            this.$emit('goBack')
        },
    },
}
</script>

<style lang="scss"></style>
