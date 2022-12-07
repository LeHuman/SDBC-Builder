<template>
    <MDBTabs v-model="activeTabId5">
        <MDBTabContent>
            <MDBTabPane id="tabView" tabId="ex5-1">
                <p class="fs-1">Messages</p>
                <MDBRow class="MDBRow">
                    <MDBCol class="selectorCont" color="primary">
                        <MDBListGroup class="selector" id="nodeSelector">
                            <MDBListGroupItem tag="a" href="#" ripple noBorder spacing action active>Cras justo odio
                            </MDBListGroupItem>
                            <MDBListGroupItem tag="a" href="#" ripple noBorder spacing action>Dapibus ac facilisis
                                in
                            </MDBListGroupItem>
                            <MDBListGroupItem tag="a" href="#" ripple noBorder spacing action>Morbi leo risus
                            </MDBListGroupItem>
                            <MDBListGroupItem tag="a" href="#" ripple noBorder spacing action>Porta ac consectetur
                                ac
                            </MDBListGroupItem>
                            <MDBListGroupItem tag="a" href="#" ripple noBorder spacing action>Vestibulum at eros
                            </MDBListGroupItem>
                        </MDBListGroup>
                    </MDBCol>
                    <MDBCol class="selectorCont" color="secondary">
                        <MDBListGroup class="selector" id="signalSelector">
                            <MDBListGroupItem tag="a" href="#" ripple noBorder spacing action>Cras justo odio
                            </MDBListGroupItem>
                            <MDBListGroupItem tag="a" href="#" ripple noBorder spacing action>Dapibus ac facilisis in
                            </MDBListGroupItem>
                            <MDBListGroupItem tag="a" href="#" ripple noBorder spacing action>Morbi leo risus
                            </MDBListGroupItem>
                            <MDBListGroupItem tag="a" href="#" ripple noBorder spacing action>Porta ac consectetur ac
                            </MDBListGroupItem>
                            <MDBListGroupItem tag="a" href="#" ripple noBorder spacing action>Vestibulum at eros
                            </MDBListGroupItem>
                            <MDBListGroupItem tag="a" href="#" ripple noBorder spacing action>Cras justo odio
                            </MDBListGroupItem>
                            <MDBListGroupItem tag="a" href="#" ripple noBorder spacing action>Dapibus ac facilisis in
                            </MDBListGroupItem>
                            <MDBListGroupItem tag="a" href="#" ripple noBorder spacing action>Morbi leo risus
                            </MDBListGroupItem>
                            <MDBListGroupItem tag="a" href="#" ripple noBorder spacing action>Porta ac consectetur ac
                            </MDBListGroupItem>
                            <MDBListGroupItem tag="a" href="#" ripple noBorder spacing action>Vestibulum at eros
                            </MDBListGroupItem>
                        </MDBListGroup>
                    </MDBCol>
                </MDBRow>

                <MDBRow class="MDBRow">
                    <MDBCol>
                        <MDBInput type="text" label="Message ID" id="form_msg_id" v-model="form_msg_id" />
                        <MDBInput class="padTop" type="text" label="Message CAN Address" id="form_msg_can"
                            v-model="form_msg_can" />
                        <MDBTextarea class="padTop" label="Message Description" rows="3" v-model="form_msg_desc" />
                    </MDBCol>
                    <MDBCol>
                        <MDBInput type="text" label="Sig ID" id="form_sig_id" v-model="form_sig_id" />
                        <select class="padTop form-control" aria-label="Signal Format">
                            <option selected>Signal Format</option>
                            <option value="1">One</option>
                            <option value="2">Two</option>
                            <option value="3">Three</option>
                        </select>
                        <MDBTextarea class="padTop" label="Signal Description" rows="3" v-model="form_sig_desc" />
                    </MDBCol>
                </MDBRow>

                <MDBRow class="MDBRow">
                    <MDBCol auto>
                        <MDBBtn class="MDBBtn" @click="newMessage" color="primary">New Message</MDBBtn>
                        <MDBBtn class="MDBBtn" @click="deleteMessage" color="primary">Delete Message</MDBBtn>
                        <MDBBtn class="MDBBtn" @click="newSignal" color="secondary">New Signal</MDBBtn>
                        <MDBBtn class="MDBBtn" @click="deleteSignal" color="secondary">Delete Signal</MDBBtn>
                    </MDBCol>
                    <MDBCol style="max-width: 50%; margin-left: auto;">
                        <MDBRow>
                            <MDBCol>
                                <MDBInput type="text" label="Scale" id="form12Input1" v-model="form12Input1" />
                            </MDBCol>
                            <MDBCol>
                                <MDBInput type="text" label="Offset" id="form12Input2" v-model="form12Input2" />
                            </MDBCol>
                            <MDBCol>
                                <MDBInput type="text" label="Min" id="form12Input1" v-model="form12Input1" />
                            </MDBCol>
                            <MDBCol>
                                <MDBInput type="text" label="Max" id="form12Input2" v-model="form12Input2" />
                            </MDBCol>
                        </MDBRow>
                    </MDBCol>
                </MDBRow>

            </MDBTabPane>
            <MDBTabPane id="tabView" tabId="ex5-2">
                <p class="fs-1">Formats</p>
            </MDBTabPane>
            <MDBTabPane id="tabView" tabId="ex5-3">
                <p class="fs-1">Types</p>
            </MDBTabPane>
            <MDBTabPane id="tabView" tabId="ex5-4">
                <p class="fs-1">Nodes</p>
            </MDBTabPane>
        </MDBTabContent>
        <MDBRow id="navRow">
            <MDBTabNav id="nav" tabsClasses="mb-3">
                <MDBTabItem tabId="ex5-1" href="ex5-1">
                    <i class="fas fa-chart-pie fa-fw me-2"></i>Messages
                </MDBTabItem>
                <MDBTabItem tabId="ex5-2" href="ex5-2">
                    <i class="fas fa-chart-line fa-fw me-2"></i>Formats
                </MDBTabItem>
                <MDBTabItem tabId="ex5-3" href="ex5-3">
                    <i class="fas fa-cogs fa-fw me-2"></i>Types
                </MDBTabItem>
                <MDBTabItem tabId="ex5-4" href="ex5-4" style="padding-right: 2rem; margin-right: 1rem;">
                    <i class="fas fa-cogs fa-fw me-2"></i>Nodes
                </MDBTabItem>
                <MDBCol auto>
                    <MDBBtn class="MDBBtn navBtn" @click="openFile">Open File</MDBBtn>
                    <MDBBtn class="MDBBtn navBtn" @click="saveFile">Save File</MDBBtn>
                </MDBCol>
                <MDBCol>
                    <MDBInput type="text" label="File Name" id="form_file_name" v-model="form_file_name" />
                </MDBCol>
            </MDBTabNav>
            <MDBTextarea style="background-color: transparent; max-height: 10vh;" readonly label="Log" rows="4"
                id="logText" v-model="logText" />
        </MDBRow>
    </MDBTabs>

</template>

<script setup lang="ts">
import {
    MDBRow, MDBCol, MDBInput, MDBSwitch, MDBBtn, MDBListGroup, MDBListGroupItem,
    MDBTabs,
    MDBTabNav,
    MDBTabContent,
    MDBTabItem,
    MDBTabPane,
    MDBTextarea,
    MDBDropdown, MDBDropdownToggle, MDBDropdownMenu, MDBDropdownItem,
} from 'mdb-vue-ui-kit';
import { ref } from "vue";

const logText = ref("SDBC Builder\n");

const form_file_name = ref("");

const form_msg_desc = ref("");
const form_sig_desc = ref("");

const activeTabId5 = ref('ex5-1');

const form_msg_id = ref("");
const form_msg_can = ref("");

const form_sig_id = ref("");

const form12Input1 = ref("");
const form12Input2 = ref("");

</script>

<script lang="ts">
export default {
    data() {
        return {
        }
    },
    methods: {
        logMsg(msg: string) {
            const form = new Intl.DateTimeFormat('en', { hour: 'numeric', minute: 'numeric', second: 'numeric' })
            logText.value = `${form.format(Date.now())} : ${msg}\n` + logText.value;
        },
        newMessage() {
            this.logMsg("New message created");
        },
        deleteMessage() {
            this.logMsg("Message deleted");
        },
        newSignal() {
            this.logMsg("New signal created");
        },
        deleteSignal() {
            this.logMsg("Signal deleted");
        },
        openFile() {
            this.logMsg("File opened");
        },
        saveFile() {
            this.logMsg("File saved");
        },
    },
};
</script>