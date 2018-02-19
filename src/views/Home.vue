<template>
    <div>
        <img style="width:150px" src="http://www.rollouthouston.com/assets/img/rollout_logo_green.png">
        <ui-tabs
            fullwidth
            type="text"
        >
            <ui-tab title="Notifications" icon="book">
                <div style="float: left; width: 400px; margin-right: 40px">
                    <h1>Current Notifications</h1>
                    <div v-for="notification in notifications"
                         style="border: solid 1px silver; border-radius: 4px; padding: 10px;">
                        <ui-icon style="float: right; color: red;">delete</ui-icon>
                        <h4>{{notification.title}}</h4>
                        <br>
                        {{notification.text}}
                        <br>
                        <a :href="notification.link">{{notification.link}}</a>
                        <br>
                        Expires: {{notification.expiresOn}}

                    </div>
                </div>
                <div style="float: right; width: 600px;">
                    <h1>Create a New Notification</h1>
                    <ui-textbox label="Title" placeholder="Enter a short title" v-model="title"></ui-textbox>
                    <ui-textbox label="Text" :multiLine="true" placeholder="Enter your message"
                                v-model="text"></ui-textbox>

                    <ui-textbox label="Link" placeholder="Link the notification will go to" v-model="link"></ui-textbox>


                    <ui-datepicker
                        icon="events"
                        placeholder="Select a date"
                        v-model="expiration"
                    >Expires On
                    </ui-datepicker>
                    <ui-switch v-model="pushNotification" disabled>Push Notification (coming soon)</ui-switch>
                    <ui-fab
                        style="float:right"
                        color="primary"
                        icon="add"
                        tooltip-position="right middle"
                        tooltip="Add Notification"
                        v-on:click="onClickAdd()"
                    ></ui-fab>
                </div>
                <div style="clear:both"></div>


            </ui-tab>

            <ui-tab title="Holidays" icon="people">
                Coming Soon
            </ui-tab>

            <ui-tab title="Suspend Services" icon="collections_bookmark">
                Coming Soon
            </ui-tab>
        </ui-tabs>
    </div>
</template>

<script lang="ts">
    import {Component, Vue} from 'vue-property-decorator';
    import addMonths from 'date-fns/add_months';
    const serverURL = 'http://api.rollouthouston.com';
    @Component({
        components: {}
    })
    export default class Home extends Vue {
        private link: String = "http://www.houstontx.gov/solidwaste/";
        private title: String = "";
        private text: String = "";
        private expiration: Date = addMonths(new Date(), 1);
        private pushNotification: Boolean = false
        private notifications: any[] = null;

        constructor() {
            super();
        }

        onClickAdd() {

            const value = {title: this.title, text: this.text, expiresOn: this.expiration, link: this.link};
            console.log('clicking add',value);
            window.fetch(serverURL + '/notifications/houston', {
                method: 'post',
                mode: 'cors',
                body: JSON.stringify(value), // must match 'Content-Type' header
            }).then(r => console.log('complete', r));
        }

        mounted() {
            window.fetch(serverURL +'/notifications/houston').then(r => r.json()).then(r => {
                console.log(r);
                this.notifications = r;
            });
        }
    }
</script>
