<script setup>
import axios from 'axios'
import { logger } from '../logger';
</script>
<script>
export default{
        props: {
            pageData:{
                type : Object,
                default : (()=>{}),
            },
            pageMetadata:{
                type : Object,
                default : (()=>{}),
            }
        },
        mounted(){
            this.pageData.src = JSON.parse(atob(process.env.VUE_APP_DATA))
            logger.debug(this.pageData)
            if (this.checkforError()) {
                var res = this.getData().then((res) => {
                    this.processResponse(res, 'success')
                }).catch((err) => {
                    this.processResponse(err, 'error')
                });

            }
        },
        methods : {
            checkforError() {
                this.pageData.status = this.pageData.src.code;
                if (this.pageData.src.code !== 200) {
                    this.pageData.isError = true;
                    this.pageData.errorMessage = this.pageData.src.message;
                    this.pageData.showSpinner = false;
                } else {
                    this.pageData.isError = false;
                    this.pageData.message = this.pageData.src.message;

                }
                return this.pageData.status === 200;
            },
            processResponse(res, msg) {
                if (msg == 'success') {
                    if (res.data.data.Results.length === 1) {
                        
                        this.pageData.model.record = res.data.data.Results[0];
                        if (this.pageData.model.record.osf_Email_Subscribed_TMS__c === "false") {
                            this.pageData.model.record.osf_Email_Subscribed_TMS__c = false;
                        }
                        if (this.pageData.model.record.osf_Email_Subscribed_TMS__c === "true") {
                            this.pageData.model.record.osf_Email_Subscribed_TMS__c = true;
                        }
                        if (this.pageData.model.record.osf_SMS_Subscribed_TMS__c === "false") {
                            this.pageData.model.record.osf_SMS_Subscribed_TMS__c = false;
                        }
                        if (this.pageData.model.record.osf_SMS_Subscribed_TMS__c === "true") {
                            this.pageData.model.record.osf_SMS_Subscribed_TMS__c = true;
                        }
                        if (this.pageData.model.record.osf_Email_Subscribed_TLS__c === "false") {
                            this.pageData.model.record.osf_Email_Subscribed_TLS__c = false;
                        }
                        if (this.pageData.model.record.osf_Email_Subscribed_TLS__c === "true") {
                            this.pageData.model.record.osf_Email_Subscribed_TLS__c = true;
                        }
                        if (this.pageData.model.record.osf_SMS_Subscribed_TLS__c === "false") {
                            this.pageData.model.record.osf_SMS_Subscribed_TLS__c = false;
                        }
                        if (this.pageData.model.record.osf_SMS_Subscribed_TLS__c === "true") {
                            this.pageData.model.record.osf_SMS_Subscribed_TLS__c = true;
                        }
                        this.pageData.model.originalRecord = JSON.parse(JSON.stringify(this.pageData.model.record));
                        if (this.pageData.model.record.osf_SMS_Subscribed_TLS__c || this.pageData.model.record
                            .osf_Email_Subscribed_TLS__c) {
                            this.pageData.model.theLadyShake = true;
                        }
                        if (this.pageData.model.record.osf_Email_Subscribed_TMS__c || this.pageData.model.record
                            .osf_SMS_Subscribed_TMS__c) {
                            this.pageData.model.theManShake = true;
                        }
                        if (this.pageData.model.record.osf_Email_Subscribed_TMS__c || this.pageData.model.record
                            .osf_Email_Subscribed_TLS__c) {
                            this.pageData.model.Email = true;
                        }
                        if (this.pageData.model.record.osf_SMS_Subscribed_TMS__c || this.pageData.model.record
                            .osf_SMS_Subscribed_TLS__c) {
                            this.pageData.model.SMS = true;
                        }
                    } else if (res.data.Results.length === 0) {
                        this.pageData.isError = true;
                        this.pageData.errorMessage = 'Record not found with subscriber key ' + this.pageData.src
                            .SubscriberKey;
                    } else {
                        this.pageData.isError = true;
                        this.pageData.errorMessage = 'Record not found with subscriber key ' + this.pageData.src
                            .SubscriberKey;
                    }
                } else if (msg === 'post-success') {
                    this.pageData.alertMessage = 'Your preferences and brand subscriptions have been updated.';
                    this.pageData.className = 'mt-1 alert alert-success alert-dismissible show';
                    var v = this;
                    this.$emit('save-complete');
                    setTimeout(function () {
                        v.pageData.className = 'mt-1 alert alert-danger alert-dismissible fade';
                    }, 2000)


                } else if (msg === 'error' || msg === 'post-error') {
                    this.pageData.alertMessage = 'An error occured. Please try again!';
                    this.pageData.className = 'mt-1  alert alert-danger alert-dismissible show';
                    var v = this;
                    setTimeout(function () {
                        v.pageData.className = 'mt-1  alert alert-danger alert-dismissible fade';
                    }, 2000)
                    return;
                }
                this.pageData.showSpinner = false;
            },
            getData() {
                return axios({
                    method: 'GET',
                    url: this.pageData.endPoint + "?SubscriberKey=" + this.pageData.src.SubscriberKey,
                    headers: {
                        'Content-Type': 'application/json',
                        'Cache-Control': 'no-cache',
                        'Pragma': 'no-cache',
                        'Expires': '0'
                    }
                }).then(function (result) {
                    return result;
                }).catch(function (error) {
                    return error;
                })
            },
            mapData(event) {
                this.pageData.model.record[event.target.id] = event.target.checked;
            },
            save(event) {
                if ((this.pageData.model.theManShake || this.pageData.model.theLadyShake) && !this.pageData.model.SMS &&
                    !this.pageData.model.Email) {
                    this.pageData.alertMessage = 'Please select a channel';
                    this.pageData.className = 'mt-1 alert alert-danger alert-dismissible show';
                    var v = this;
                    setTimeout(function () {
                        v.pageData.className = 'mt-1 alert alert-danger alert-dismissible fade';
                    }, 2000)
                    return;
                }
                if (!this.pageData.model.theManShake && !this.pageData.model.theLadyShake && (this.pageData.model.SMS ||
                        this.pageData.model.Email)) {
                    this.pageData.alertMessage = 'Please select a brand';
                    this.pageData.className = 'mt-1 alert alert-danger alert-dismissible show';
                    var v = this;
                    setTimeout(function () {
                        v.pageData.className = 'mt-1 alert alert-danger alert-dismissible fade';
                    }, 2000)
                    return;

                }
                if (this.pageData.model.theManShake) {
                    this.pageData.model.record.osf_SMS_Subscribed_TMS__c = this.pageData.model.SMS;
                    this.pageData.model.record.osf_Email_Subscribed_TMS__c = this.pageData.model.Email;
                } else {
                    this.pageData.model.record.osf_SMS_Subscribed_TMS__c = false;
                    this.pageData.model.record.osf_Email_Subscribed_TMS__c = false;
                }
                if (this.pageData.model.theLadyShake) {
                    this.pageData.model.record.osf_SMS_Subscribed_TLS__c = this.pageData.model.SMS;
                    this.pageData.model.record.osf_Email_Subscribed_TLS__c = this.pageData.model.Email;
                } else {
                    this.pageData.model.record.osf_SMS_Subscribed_TLS__c = false;
                    this.pageData.model.record.osf_Email_Subscribed_TLS__c = false;
                }
                this.pageData.model.record.et4ae5__HasOptedOutOfMobile__c = !(this.pageData.model.record
                    .osf_SMS_Subscribed_TMS__c || this.pageData.model.record.osf_SMS_Subscribed_TLS__c);
                this.pageData.model.record.HasOptedOutOfEmail = !(this.pageData.model.record
                    .osf_Email_Subscribed_TMS__c || !this.pageData.model.record.osf_email_Subscribed_TLS__c);

                if(this.pageData.model.record.osf_SMS_Subscribed_TMS__c !== this.pageData.model.originalRecord.osf_SMS_Subscribed_TMS__c ){
                  if(this.pageData.model.record.osf_SMS_Subscribed_TMS__c){
                    this.pageData.model.record.osf_Date_SMS_Subscribed_TMS__c = new Date().toISOString();
                  }
                  else{
                    this.pageData.model.record.osf_Date_SMS_Subscribed_TMS__c = '';
                  }
                  
                }
                if(this.pageData.model.record.osf_Email_Subscribed_TMS__c !== this.pageData.model.originalRecord.osf_Email_Subscribed_TMS__c ){
                  if(this.pageData.model.record.osf_Email_Subscribed_TMS__c){
                    this.pageData.model.record.osf_Date_Email_Subscribed_TMS__c = new Date().toISOString();
                  }else{
                    this.pageData.model.record.osf_Date_Email_Subscribed_TMS__c = '';
                  }
                }
                if(this.pageData.model.record.osf_SMS_Subscribed_TLS__c !== this.pageData.model.originalRecord.osf_SMS_Subscribed_TLS__c ){
                  if(this.pageData.model.record.osf_SMS_Subscribed_TLS__c){
                    this.pageData.model.record.osf_Date_SMS_Subscribed_TLS__c = new Date().toISOString();
                  }else{
                    this.pageData.model.record.osf_Date_SMS_Subscribed_TLS__c = '';
                  }
                  
                }
                if(this.pageData.model.record.osf_Email_Subscribed_TLS__c !== this.pageData.model.originalRecord.osf_Email_Subscribed_TLS__c ){
                  if(this.pageData.model.record.osf_Email_Subscribed_TLS__c){
                    this.pageData.model.record.osf_Date_Email_Subscribed_TLS__c = new Date().toISOString();
                  }else{
                    this.pageData.model.record.osf_Date_Email_Subscribed_TLS__c = '';
                  }
                }
                this.sendData().then((response) => {
                    this.processResponse(response, 'post-success')
                }).catch((err) => {
                    this.processResponse(err, 'post-error')
                })
            },
            unsub(event) {
                this.pageData.showSpinner = true;
                this.pageData.model.record.osf_Email_Subscribed_TMS__c = false;
                this.pageData.model.record.osf_SMS_Subscribed_TMS__c = false;
                this.pageData.model.record.osf_Email_Subscribed_TLS__c = false;
                this.pageData.model.record.osf_SMS_Subscribed_TLS__c = false;
                this.pageData.model.record.et4ae5__HasOptedOutOfMobile__c = true;
                this.pageData.model.record.HasOptedOutOfEmail = true;
                this.pageData.model.record.osf_Date_SMS_Subscribed_TMS__c = '';
                this.pageData.model.record.osf_Date_Email_Subscribed_TMS__c = '';
                this.pageData.model.record.osf_Date_SMS_Subscribed_TLS__c = '';
                this.pageData.model.record.osf_Date_Email_Subscribed_TLS__c = '';
                this.sendData().then((response) => {
                    this.processResponse(response, 'post-success');

                }).catch((err) => {
                    this.processResponse(err, 'post-error')
                })
            },
            brandMap(event) {},
            closeAlert(event) {
                this.pageData.className = 'mt-1 alert alert-danger alert-dismissible fade';
            },
            sendData() {
                this.showSpinner = true;
                return axios({
                    method: 'POST',
                    url: this.pageData.endPoint,
                    headers: {
                        'Content-Type': 'application/json',
                        'Cache-Control': 'no-cache',
                        'Pragma': 'no-cache',
                        'Expires': '0'
                    },
                    data: this.pageData.model.record
                }).then((result) => {
                    return result;
                }).catch((err) => {
                    return err;
                })
            }
        }
    }
</script>
<template>
    <div class="container">
        <div :class="pageData.className" role="alert">
            {{pageData.alertMessage}}
        </div>
        <div class="col-sm-12 col-md-12 col-lg-6 col-xl-6" style="margin:auto">
          <div class="col-12">
               <div v-if="pageData.showSpinner">
                  <div class="col-12 col-sm-12 col-md-12 col-lg-12 col-xl-12">
                    <div class="row">
                      <div style="margin:auto" class="spinner-border text-primary" role="status">
                        <span class="sr-only"></span>
                     </div>
                    </div> 
                 </div>
               </div>
               <div v-if="!pageData.showSpinner">
                <div class="col-12 col-sm-12 col-md-12 col-lg-12 col-xl-12" v-if="!pageData.isError">
                   <div class="row">
                     <div  class="col-12 col-sm-12 col-md-12 col-lg-12 col-xl-6">
                        <p class="lead">
                          <strong>{{pageMetadata.content.title}}</strong>
                        </p>
                     </div>
                   </div>
                   <hr class="hr">
                   <br/>
                   <div class="row">
                      <div class="col-12 col-sm-12 col-md-12 col-lg-12 col-xl-6">
                        <div class="form-group" >
                          <label for="fname">First Name</label>
                          <input type="text" class="form-control" id="fname" name="fname" v-model="pageData.model.record.FirstName" placeholder="Enter First Name">
                        </div>
                     </div>
                     <div class="col-12 col-sm-12 col-md-12 col-lg-6 col-xl-6">
                       <div class="form-group">
                        <label for="lname">Last Name</label>
                        <input type="text" class="form-control" id="lname" name="lname" v-model="pageData.model.record.LastName" placeholder="Enter Last Name">
                      </div>
                     </div>
                   </div>
                   <div class="row mt-4">
                      <div class="col-12 col-sm-12 col-md-12 col-lg-6 col-xl-6">
                          <div class="form-group">
                          <label for="email">Email Address</label>
                          <input type="email" disabled="true" class="form-control" id="email" name="email" v-model="pageData.model.record.Email" placeholder="Enter Email" readonly="true">
                        </div>
                     </div>
                     <div class="col-12 col-sm-12 col-md-12 col-lg-6 col-xl-6">
                       <div class="form-group">
                          <label for="phone">Mobile Phone</label>
                          <input type="phone" class="form-control" disabled="true" id="phone" name="phone" v-model="pageData.model.record.MobilePhone" readonly="true">
                        </div>
                     </div>
                   </div>
                   <br/>
                   <hr class="hr">
                   <div class="row mt-4">
                     <div  class="col-12 col-sm-12 col-md-12 col-lg-12 col-xl-12">
                        <p class="lead">
                          <strong>{{pageMetadata.brandSetup.title}}</strong>
                        </p>
                     </div>
                   </div>
                   <div class="row mt-4">
                     <div  class="col-12 col-sm-12 col-md-12 col-lg-12 col-xl-12">
                       <p class="">
                          {{pageMetadata.brandSetup.body}}
                       </p>
                     </div>
                   </div>
                   <div class="row mt-4">
                     <div  class="col-12 col-sm-12 col-md-12 col-lg-12 col-xl-12">
                       <p class="">
                          {{pageMetadata.brandSetup.baseQuestion}}
                       </p>
                     </div>
                   </div>
                   <div class="row mt-2" v-for="brand in pageMetadata.brandSetup.brands">
                     <div class="col-12 col-sm-12 col-md-12 col-lg-12 col-xl-12">
                       <div class="row">
                         <div  class="col-12 col-sm-12 col-md-12 col-lg-12 col-xl-12">
                           <div class="form-group preferences">
                               <input @change="brandMap($event)" type="checkbox" :id="brand.name" :name="brand.name" v-model="pageData.model[brand.target]"/>
                               <label :for="brand.name">{{ brand.name }}</label>
                           </div>
                         </div>
                          
                       </div>
                     </div>
                   </div>
                   <div class="row mt-4">
                     <div  class="col-12 col-sm-12 col-md-12 col-lg-12 col-xl-12">
                       <p class="">
                          {{pageMetadata.brandSetup.subQuestion}}
                       </p>
                     </div>
                   </div>
                   <div class="row mt-2" v-for="channel in pageMetadata.brandSetup.channel_options">
                     <div class="col-12 col-sm-12 col-md-12 col-lg-12 col-xl-12" style="margin-left:5:">
                       <div class="row">
                         <div  class="col-12 col-sm-12 col-md-12 col-lg-12 col-xl-12">
                           <div class="form-group preferences" style="margin-bottom:1%">
                             <input type="checkbox" :id="channel.target" :name="channel.name" v-model="pageData.model[channel.target]"/>
                             <label :for="channel.name">{{ channel.name }}</label>
                           </div>
                         </div>

                       </div>
                     </div>
                   </div>
                  <div class="row mt-3">
                   <div class="col-12">
                     <!-- <span v-html="pageMetadata.footer.text"/> -->
                   </div>
                 </div>
                 <hr class="hr">
                 <div class="row">
                   <div class="col-6">
                     <div style="display:flex;justify-content:center">
                       <button @click="save($event)" :class="pageMetadata.buttons.primaryButton.class" :style="pageMetadata.buttons.primaryButton.style" :label="pageMetadata.buttons.primaryButton.label">{{pageMetadata.buttons.primaryButton.label}}</button>
                     </div>
                   </div>
                   <div class="col-6">
                     <div style="display:flex;justify-content:center">
                       <button @click="unsub($event)" :class="pageMetadata.buttons.secondaryButton.class" :style="pageMetadata.buttons.secondaryButton.style" :label="pageMetadata.buttons.secondaryButton.label">{{pageMetadata.buttons.secondaryButton.label}}</button>
                     </div>
                   </div>
                 </div>
                </div>
                <hr class="hr">
                <div class="col-12 col-sm-12 col-md-12 col-lg-12 col-xl-12" v-if="pageData.isError">
                  <div class="alert alert-danger" role="alert">
                    {{pageData.errorMessage}}
                  </div>
                </div>
                 <div class="row" style="height:50px">
                   </div>
              </div>
          </div>
        </div>
    </div>
    
</template>