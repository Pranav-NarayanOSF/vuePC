<script setup>
    import headerComponent from '../components/header.vue';
    import preferenceComponent from '../components/preferenceComponent.vue';
    import axios from 'axios'
    import footerComponent from '../components/footer.vue';
    import { logger } from '../logger';

</script>
<script>
    export default {
        data() {
            return {
                componentKey: 0,
                pageData: {
                    "src": atob(process.env.VUE_APP_DATA),
                    "status": 100,
                    "showSpinner": true,
                    "errorMessage": '',
                    "className": "mt-2 alert alert-danger alert-dismissible fade",
                    "message": "",
                    "alertMessage": "",
                    "model": {
                        "record": {},
                        "originalRecord" : {},
                        "checkedBrands": [],
                        "checkedChannels": [],
                        "unsubscribeall": false
                    },
                    "isError": false,
                    response: {},
                    endPoint: "https://cloud.email.crankyhealth.com.au/backend_processing"
                },
                pageMetadata: {
                    content: {
                        title: "MANAGE YOUR PREFERENCES",
                    },
                    brandSetup: {
                        "baseQuestion": "I would like to hear from",
                        "body": "We send out regular emails with tips and tricks on how to get the most out of our shakes, as well as promotions, and send out our best efforts on SMS.",
                        "title": "SUBSCRIPTION PREFERENCES",
                        "subQuestion": "How?",
                        "brands": [{
                                "name": "The Lady Shake",
                                "value": false,
                                "target": "theLadyShake",
                            },
                            {
                                "name": "The Man Shake",
                                "value": false,
                                "target": "theManShake",
                            }
                        ],
                        "channel_options": [{
                            "name": "Email",
                            "value": false,
                            "target": "Email"
                        }, {
                            "name": "SMS",
                            "value": "false",
                            "target": "SMS"
                        }]
                    },
                    "footer": {
                        "text": "By opting in you consent to the Cranky Health <a href='' >terms and conditions</a>"
                    },
                    "buttons": {
                        "primaryButton": {
                            "label": "Update Preferences",
                            "style": "",
                            "class": "btn btn-primary"
                        },
                        "secondaryButton": {
                            "label": "Unsubscribe from everything",
                            "style": "",
                            "class": "btn btn-secondary"
                        }
                    },
                    "header_images": [{
                        "src": "https://image.email.crankyhealth.com.au/lib/fe3311737164047a741577/m/1/d70ace07-78f8-43c1-819a-91b7fe19b3a7.png",
                        "alt": "logo"
                    }]
                }
            }
        },
        mounted() {
            
        },
        methods : {
            handleSave(evt){
                this.componentKey += 1;
            }   
        }
    }
</script>
<template>
    <headerComponent v-bind:pageData="pageData" v-bind:pageMetadata="pageMetadata"></headerComponent>
    <preferenceComponent @save-complete="handleSave" :key="componentKey"  v-bind:pageData="pageData" v-bind:pageMetadata="pageMetadata"></preferenceComponent>
    <footerComponent v-bind:pageData="pageData" v-bind:pageMetadata="pageMetadata"></footerComponent>
</template>