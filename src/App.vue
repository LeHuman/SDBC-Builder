<template>
    <MDBTabs v-model="activeTabId5">
        <MDBTabContent>
            <MDBTabPane id="tabView" tabId="ex5-1" tag="form" novalidate @submit.prevent="checkForm">
                <p class="fs-1">SDBC Builder v2</p>
                <MDBRow>
                    <MDBCol>
                        <p class="fs-4">Nodes</p>
                    </MDBCol>
                    <MDBCol>
                        <p class="fs-4">Messages</p>
                    </MDBCol>
                    <MDBCol>
                        <p class="fs-4">Signals</p>
                    </MDBCol>
                </MDBRow>
                <MDBRow>
                    <MDBCol class="selectorCont">
                        <MDBListGroup class="selector" id="nodeSelector">
                            <MDBListGroupItem v-for="node in nodes" :key="node.name"
                                v-bind:active="(activeNode === node)" @click="selectNode(node)" ripple noBorder spacing
                                action>
                                {{ node.getDesc() }}
                            </MDBListGroupItem>
                        </MDBListGroup>
                    </MDBCol>
                    <MDBCol class="selectorCont" color="primary">
                        <MDBListGroup class="selector" id="msgSelector">
                            <MDBListGroupItem v-for="msg in activeNode?.messages" :key="msg.name"
                                v-bind:active="(activeMsg === msg)" @click="selectMessage(msg)" ripple noBorder spacing
                                action>
                                {{ msg.getDesc() }}
                            </MDBListGroupItem>
                        </MDBListGroup>
                    </MDBCol>
                    <MDBCol class="selectorCont" color="secondary">
                        <MDBListGroup class="selector" id="signalSelector">
                            <MDBListGroupItem v-for="sig in activeMsg?.signals" :key="sig.name"
                                v-bind:active="(activeSig === sig)" @click="selectSignal(sig)" ripple noBorder spacing
                                action>
                                {{ sig.getDesc() }}
                            </MDBListGroupItem>
                        </MDBListGroup>
                    </MDBCol>
                </MDBRow>
                <MDBRow class="MDBRow">
                    <MDBCol>
                        <MDBTooltip v-model="tooltip0" :arrow="(true)" :disabled="(activeMsg != null)" direction="top">
                            <template #reference>
                                <MDBRow class="g-3">
                                    <MDBCol col="50">
                                        <MDBInput type="text" v-bind:is-valid="validName(activeMsg?.name)"
                                            :disabled="(activeMsg === null)"
                                            v-bind:is-validated="(!validName(activeMsg?.name) && activeMsg != null)"
                                            required :invalidFeedback="(helpStr_ID)" label="Message ID"
                                            v-bind:model-value="activeMsg?.name"
                                            @update:model-value="val => activeMsg ? activeMsg.name = val : false" />
                                    </MDBCol>
                                    <MDBCol col="50">
                                        <MDBInput v-bind:is-valid="validAddress(activeMsg?.address)"
                                            :disabled="(activeMsg === null)"
                                            v-bind:is-validated="(!validAddress(activeMsg?.address) && activeMsg != null)"
                                            required :invalidFeedback="(helpStr_Addr)" type="text"
                                            label="Message CAN Address"
                                            v-bind:model-value="activeMsg?.address?.toString()"
                                            @update:model-value="val => activeMsg ? activeMsg.address = val : false" />
                                    </MDBCol>
                                    <MDBCol col="50">
                                        <MDBTextarea :disabled="(activeMsg === null)" type="text"
                                            label="Message Description" rows="2" style="max-height: 5rem;"
                                            v-bind:model-value="activeMsg?.desc"
                                            @update:model-value="val => activeMsg ? activeMsg.desc = val : false" />
                                    </MDBCol>
                                    <MDBCol col="50">
                                        <MDBSwitch :disabled="(activeMsg === null)"
                                            :label="activeMsg?.incoming ? 'Incoming Message' : 'Outgoing message'"
                                            v-bind:model-value="activeMsg?.incoming"
                                            @update:model-value="val => activeMsg ? activeMsg.incoming = val : false" />
                                    </MDBCol>
                                </MDBRow>
                            </template>
                            <template #tip>
                                Select a Message First
                            </template>
                        </MDBTooltip>
                    </MDBCol>
                    <MDBCol>
                        <MDBTooltip v-model="tooltip1" :arrow="(true)" :disabled="(activeSig != null)" direction="top">
                            <template #reference>
                                <MDBRow class="g-3">
                                    <MDBCol col="50">
                                        <MDBInput :disabled="(activeSig === null)" type="text"
                                            v-bind:is-valid="validName(activeSig?.name)"
                                            v-bind:is-validated="(!validName(activeSig?.name) && activeSig != null)"
                                            required :invalidFeedback="(helpStr_ID)" label="Sig ID"
                                            v-bind:model-value="activeSig?.name"
                                            @update:model-value="val => activeSig ? activeSig.name = val : false" />
                                    </MDBCol>
                                    <!-- TODO: validation for selector -->
                                    <MDBCol col="50">
                                        <vSelect :class="activeSig ? (activeSig?.format ? '' : 'vSelectError') : ''"
                                            placeholder="Select a Signal Format" class="vSelect" :options="(formats)"
                                            label="name" :disabled="(activeSig === null)"
                                            v-bind:model-value="activeSig?.format"
                                            @update:model-value="val => activeSig ? activeSig.format = val : false">
                                        </vSelect>
                                    </MDBCol>
                                    <MDBCol col="50">
                                        <MDBTextarea :disabled="(activeSig === null)" type="text"
                                            label="Signal Description" rows="2" style="max-height: 5rem;"
                                            v-bind:model-value="activeSig?.desc"
                                            @update:model-value="val => activeSig ? activeSig.desc = val : false" />
                                    </MDBCol>
                                    <MDBCol col="50">
                                        <MDBSwitch :disabled="(activeSig === null)"
                                            :label="activeMsg?.incoming ? (activeSig?.setter ? `Is set by value ${activeSig.setter.gpio?.getDesc()}` : 'No Back Setter') : (activeSig?.setter ? `Sets value ${activeSig.setter.gpio?.getDesc()}` : 'No Forward Setter')" />
                                    </MDBCol>
                                </MDBRow>
                            </template>
                            <template #tip>
                                Select a Signal First
                            </template>
                        </MDBTooltip>
                    </MDBCol>
                </MDBRow>
                <MDBRow class="MDBRow">
                    <MDBCol auto>
                        <MDBBtn class="MDBBtn" @click="newMessage" color="primary">New Message</MDBBtn>
                        <MDBBtn class="MDBBtn" @click="deleteMessage" color="primary">Delete Message</MDBBtn>
                        <MDBBtn class="MDBBtn" @click="newSignal" color="secondary">New Signal</MDBBtn>
                        <MDBBtn class="MDBBtn" @click="deleteSignal" color="secondary">Delete Signal</MDBBtn>
                    </MDBCol>
                </MDBRow>
            </MDBTabPane>
            <MDBTabPane id="tabView" tabId="ex5-2">
                <p class="fs-1">Formats</p>
                <MDBRow>
                    <MDBCol md="4">
                        <p class="fs-4">Select</p>
                    </MDBCol>
                    <MDBCol>
                        <p class="fs-4">Config</p>
                    </MDBCol>
                </MDBRow>
                <MDBRow>
                    <MDBCol md="4">
                        <div class="selectorContAlt">
                            <MDBListGroup class="selector">
                                <MDBListGroupItem v-for="form in formats" :key="form.name"
                                    v-bind:active="(activeFormat === form)" @click="(activeFormat = form)" ripple
                                    noBorder spacing action>
                                    {{ form.getDesc() }}
                                </MDBListGroupItem>
                            </MDBListGroup>
                        </div>
                        <MDBCol class="MDBRow">
                            <MDBBtn class="MDBBtn" @click="newFormat" color="primary">New Format</MDBBtn>
                            <MDBBtn class="MDBBtn" @click="deleteFormat" color="primary">Delete Format</MDBBtn>
                        </MDBCol>
                    </MDBCol>
                    <MDBCol>
                        <MDBTooltip v-model="tooltip2" :arrow="(true)" :disabled="(activeFormat != null)"
                            direction="top">
                            <template #reference>
                                <MDBRow class="g-3">
                                    <MDBCol col="50">
                                        <MDBInput v-bind:is-valid="validName(activeFormat?.name)"
                                            :disabled="(activeFormat === null)"
                                            v-bind:is-validated="(!validName(activeFormat?.name) && activeFormat != null)"
                                            required :invalidFeedback="(helpStr_ID)" type="text" label="Format ID"
                                            v-bind:model-value="activeFormat?.name"
                                            @update:model-value="val => activeFormat ? activeFormat.name = val : false" />
                                    </MDBCol>
                                    <MDBCol col="50">
                                        <MDBTextarea label="Format Description" rows="2" style="max-height: 5rem;"
                                            :disabled="(activeFormat === null)" v-bind:model-value="activeFormat?.desc"
                                            @update:model-value="val => activeFormat ? activeFormat.desc = val : false" />
                                    </MDBCol>
                                    <MDBCol col="3">
                                        <MDBInput :invalidFeedback="(helpStr_Num)" :disabled="(activeFormat === null)"
                                            v-bind:is-valid="validNumber(activeFormat?.scale)"
                                            v-bind:is-validated="(!validNumber(activeFormat?.scale) && activeFormat != null)"
                                            required type="text" label="Scale"
                                            v-bind:model-value="activeFormat?.scale?.toString()"
                                            @update:model-value="val => activeFormat ? activeFormat.scale = val : false" />
                                    </MDBCol>
                                    <MDBCol col="3">
                                        <MDBInput :invalidFeedback="(helpStr_Num)" :disabled="(activeFormat === null)"
                                            v-bind:is-valid="validNumber(activeFormat?.offset)"
                                            v-bind:is-validated="(!validNumber(activeFormat?.offset) && activeFormat != null)"
                                            required type="text" label="Offset"
                                            v-bind:model-value="activeFormat?.offset?.toString()"
                                            @update:model-value="val => activeFormat ? activeFormat.offset = val : false" />
                                    </MDBCol>
                                    <MDBCol col="3">
                                        <MDBInput
                                            :invalidFeedback="(validNumber(activeFormat?.min) ? helpStr_Rng : helpStr_Num)"
                                            :disabled="(activeFormat === null)"
                                            v-bind:is-valid="(validNumber(activeFormat?.min) && validRange(activeFormat?.min, activeFormat?.max))"
                                            v-bind:is-validated="((!(validNumber(activeFormat?.min) && validRange(activeFormat?.min, activeFormat?.max))) && activeFormat != null)"
                                            required type="text" label="Min"
                                            v-bind:model-value="activeFormat?.min?.toString()"
                                            @update:model-value="val => activeFormat ? activeFormat.min = val : false" />
                                    </MDBCol>
                                    <MDBCol col="3">
                                        <MDBInput
                                            :invalidFeedback="(validNumber(activeFormat?.max) ? helpStr_Rng : helpStr_Num)"
                                            :disabled="(activeFormat === null)"
                                            v-bind:is-valid="(validNumber(activeFormat?.max) && validRange(activeFormat?.min, activeFormat?.max))"
                                            v-bind:is-validated="((!(validNumber(activeFormat?.max) && validRange(activeFormat?.min, activeFormat?.max))) && activeFormat != null)"
                                            required type="text" label="Max"
                                            v-bind:model-value="activeFormat?.max?.toString()"
                                            @update:model-value="val => activeFormat ? activeFormat.max = val : false" />
                                    </MDBCol>
                                </MDBRow>
                            </template>
                            <template #tip>
                                Select a Format First
                            </template>
                        </MDBTooltip>
                    </MDBCol>
                </MDBRow>
            </MDBTabPane>
            <MDBTabPane id="tabView" tabId="ex5-3">
                <p class="fs-1">Types</p>
                <MDBRow>
                    <MDBCol md="4">
                        <p class="fs-4">Select</p>
                    </MDBCol>
                    <MDBCol>
                        <p class="fs-4">Config</p>
                    </MDBCol>
                </MDBRow>
                <MDBRow>
                    <MDBCol md="4">
                        <div class="selectorContAlt">
                            <MDBListGroup class="selector">
                                <MDBListGroupItem v-for="dtype in dTypes" :key="dtype.name"
                                    v-bind:active="(activeDType === dtype)" @click="(activeDType = dtype)" ripple
                                    noBorder spacing action>
                                    {{ dtype.getDesc() }}
                                </MDBListGroupItem>
                            </MDBListGroup>
                        </div>
                        <MDBCol class="MDBRow">
                            <MDBBtn class="MDBBtn" @click="newDType" color="primary">New Type</MDBBtn>
                            <MDBBtn class="MDBBtn" @click="deleteDType" color="primary">Delete Type</MDBBtn>
                        </MDBCol>
                    </MDBCol>
                    <MDBCol>
                        <MDBRow class="g-3">
                            <MDBCol col="50">
                                <MDBInput v-bind:is-valid="validName(activeDType?.name)"
                                    v-bind:is-validated="!validName(activeDType?.name)" required
                                    :invalidFeedback="(helpStr_ID)" type="text" label="Format ID"
                                    v-bind:model-value="activeDType?.name"
                                    @update:model-value="val => activeDType ? activeDType.name = val : false" />
                            </MDBCol>
                            <MDBCol col="50">
                                <MDBTextarea label="Format Description" rows="2" style="max-height: 5rem;"
                                    v-bind:model-value="activeDType?.desc"
                                    @update:model-value="val => activeDType ? activeDType.desc = val : false" />
                            </MDBCol>
                        </MDBRow>
                    </MDBCol>
                </MDBRow>
            </MDBTabPane>
            <MDBTabPane id="tabView" tabId="ex5-4">
                <p class="fs-1">Nodes</p>
                <MDBRow>
                    <MDBCol md="4">
                        <p class="fs-4">Select</p>
                    </MDBCol>
                    <MDBCol>
                        <p class="fs-4">Config</p>
                    </MDBCol>
                </MDBRow>
                <MDBRow>
                    <MDBCol md="4">
                        <div class="selectorContAlt">
                            <MDBListGroup class="selector">
                                <MDBListGroupItem v-for="node in nodes" :key="node.name"
                                    v-bind:active="(activeNode === node)" @click="selectNode(node)" ripple noBorder
                                    spacing action>
                                    {{ node.getDesc() }}
                                </MDBListGroupItem>
                            </MDBListGroup>
                        </div>
                        <MDBCol class="MDBRow">
                            <MDBBtn class="MDBBtn" @click="newNode" color="primary">New Node</MDBBtn>
                            <MDBBtn class="MDBBtn" @click="deleteNode" color="primary">Delete Node</MDBBtn>
                        </MDBCol>
                    </MDBCol>
                    <MDBCol>
                        <MDBRow class="g-3">
                            <MDBCol col="50">
                                <MDBInput v-bind:is-valid="validName(activeNode?.name)"
                                    v-bind:is-validated="!validName(activeNode?.name)" required
                                    :invalidFeedback="(helpStr_ID)" type="text" label="Node ID"
                                    v-bind:model-value="activeNode?.name"
                                    @update:model-value="val => activeNode ? activeNode.name = val : false" />
                            </MDBCol>
                            <MDBCol col="50">
                                <MDBTextarea label="Node Description" rows="2" style="max-height: 5rem;"
                                    v-bind:model-value="activeNode?.desc"
                                    @update:model-value="val => activeNode ? activeNode.desc = val : false" />
                            </MDBCol>
                        </MDBRow>
                        <p class="fs-4" style="margin-top: 1rem;">Address Range</p>
                        <MDBRow class="g-3">
                            <MDBCol md="5">
                                <MDBRow>
                                    <p type="fs-5">Set Addresses</p>
                                    <div class="selectorCont">
                                        <MDBListGroup class="selector">
                                            <MDBListGroupItem v-for="num in activeNode?.addresses"
                                                @click="(activeAddr = num)" :key="num.valueOf()"
                                                v-bind:active="(activeAddr === num)" ripple noBorder spacing action>
                                                {{ num.toString() }}
                                            </MDBListGroupItem>
                                        </MDBListGroup>
                                    </div>
                                </MDBRow>
                                <MDBRow class="MDBRow">
                                    <p type="fs-5">Inferred Addresses</p>
                                    <div class="selectorCont">
                                        <MDBListGroup class="selector">
                                            <MDBListGroupItem v-for="msg in activeNode?.messages" :key="msg.name"
                                                v-bind:active="(activeMsg === msg)" @click="selectMessage(msg)" ripple
                                                noBorder spacing action>
                                                {{ `${msg.address} : ${msg.name}` }}
                                            </MDBListGroupItem>
                                        </MDBListGroup>
                                    </div>
                                </MDBRow>
                            </MDBCol>
                            <MDBCol style="margin-left: 1rem;">
                                <MDBRow class="g-3">
                                    <MDBCol col="50">
                                        <MDBInput v-bind:is-valid="validAddress(nodeRangeModMin)"
                                            v-bind:is-validated="!validAddress(nodeRangeModMin)" required
                                            :invalidFeedback="(helpStr_Addr)" type="text" label="Range Min"
                                            @update:model-value="val => nodeRangeModMin = Number(val)" />
                                    </MDBCol>
                                    <MDBCol col="50">
                                        <MDBInput v-bind:is-valid="validAddress(nodeRangeModMax)"
                                            v-bind:is-validated="!validAddress(nodeRangeModMax)" required
                                            :invalidFeedback="(helpStr_Addr)" type="text" label="Range Max"
                                            @update:model-value="val => nodeRangeModMax = Number(val)" />
                                    </MDBCol>
                                    <MDBCol col="50">
                                        <MDBBtn class="MDBBtn" @click="addRange" color="primary">Add Range</MDBBtn>
                                        <MDBBtn class="MDBBtn" @click="removeRange" color="primary">Remove Range
                                        </MDBBtn>
                                    </MDBCol>
                                </MDBRow>
                            </MDBCol>
                        </MDBRow>
                    </MDBCol>
                </MDBRow>
            </MDBTabPane>
        </MDBTabContent>
        <MDBRow id="navRow">
            <MDBTabNav id="nav" tabsClasses="mb-3">
                <MDBTabItem tabId="ex5-1" href="ex5-1">
                    <i class="fas fa-chart-pie fa-fw me-2"></i>Builder
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
                    <!-- <MDBBtn class="MDBBtn navBtn" @click="openFile">Open File</MDBBtn> -->
                    <MDBBtn class="MDBBtn navBtn" @click="saveFile">Save File</MDBBtn>
                </MDBCol>
                <MDBCol>
                    <!-- <MDBInput dark placeholder="No file loaded"></MDBInput> -->
                    <MDBFile dark id="form_file" v-model="form_file" />
                </MDBCol>
            </MDBTabNav>
            <MDBTextarea style="background-color: transparent; max-height: 10vh;" readonly label="Log" rows="4"
                id="logText" v-model="logText" />
        </MDBRow>
    </MDBTabs>
</template>

<style scoped>
.vSelect {

    min-width: 45vh;

    /* Disabled State */
    --vs-state-disabled-bg: #262626;
    --vs-state-disabled-color: #262626;
    --vs-state-disabled-controls-color: #262626;
    --vs-state-disabled-cursor: not-allowed;

    --vs-controls-color: #bbbbbb;
    --vs-border-color: #bbbbbb;

    --vs-dropdown-bg: #262626;
    --vs-dropdown-color: #bbbbbb;
    --vs-dropdown-option-color: #bbbbbb;

    --vs-selected-bg: #bbbbbb;
    --vs-selected-color: #bbbbbb;

    --vs-search-input-color: #bbbbbb;

    --vs-dropdown-option--active-bg: #bbbbbb;
    --vs-dropdown-option--active-color: #262626;
}

.vSelectError {
    --vs-border-color: #dc4c64;
    --vs-controls-color: #dc4c64;
    --vs-search-input-placeholder-color: #dc4c64;
}
</style>

<script setup lang="ts">
import {
    MDBRow, MDBCol, MDBInput, MDBSwitch, MDBBtn, MDBListGroup, MDBListGroupItem,
    MDBTabs,
    MDBTabNav,
    MDBTabContent,
    MDBTabItem,
    MDBTabPane,
    MDBTextarea,
    MDBFile,
    MDBTooltip
} from 'mdb-vue-ui-kit';
import { ref } from "vue";
import vSelect from 'vue-select'

const logText = ref("Hallo :)\n");

const form_file = ref([]);

const activeTabId5 = ref('ex5-1');

const form12Input1 = ref("");
const form12Input2 = ref("");

const tooltip0 = ref(false);
const tooltip1 = ref(false);
const tooltip2 = ref(false);
const tooltip3 = ref(false);
</script>

<script lang="ts">

class BaseType {
    name: string;
    desc: string | undefined;
    aux: string;
    constructor(name: string = 'nil', desc: string | undefined = undefined) {
        this.name = name;
        this.desc = desc;
    }
    getDesc(limit: number = 32, withName: boolean = true) {
        return `${withName ? (this.name ? this.name : '$nil') : ''}${this.desc ? ` : ${this.desc.slice(0, limit)}${this.desc.length > limit ? '...' : ''}` : ''}`;
    }
}

class dType extends BaseType {
    signed: boolean | undefined;
    bits: Number | undefined;
    isFloating: boolean | undefined;
    constructor(name: string = 'nil', signed: boolean | undefined = undefined, bits: Number | undefined = undefined, isFloating: boolean | undefined = undefined, desc: string | undefined = undefined) {
        super(name, desc);
        this.signed = signed;
        this.bits = bits;
        this.isFloating = isFloating;
    }
}

class Format extends BaseType {
    dType: dType | undefined;
    scale: Number | undefined;
    offset: Number | undefined;
    min: Number | undefined;
    max: Number | undefined;
    constructor(name: string = 'nil', dType: dType | undefined = undefined, scale: Number | undefined = undefined, offset: Number | undefined = undefined, min: Number | undefined = undefined, max: Number | undefined = undefined, desc: string | undefined = undefined) {
        super(name, desc);
        this.dType = dType;
        this.scale = scale;
        this.offset = offset;
        this.min = min;
        this.max = max;
    }
}

class GPIO extends BaseType {
    pin: Number | undefined;
    isDigital: boolean | undefined;
    isOutput: boolean | undefined;
    isVirt: boolean | undefined;
    constructor(name: string = 'nil', pin: Number | undefined = undefined, isDigital: boolean | undefined = undefined, isOutput: boolean | undefined = undefined, isVirt: boolean | undefined = undefined, desc: string | undefined = undefined) {
        super(name, desc);
        this.pin = pin;
        this.isDigital = isDigital;
        this.isOutput = isOutput;
        this.isVirt = isVirt;
    }
}

class Setter {
    incoming: boolean | undefined;
    gpio: GPIO | undefined;
    desc: string | undefined;
    constructor(incoming: boolean | undefined = undefined, gpio: GPIO | undefined = undefined, desc: string | undefined = undefined) {
        this.incoming = incoming;
        this.gpio = gpio;
        this.desc = desc;
    }
}

class Signal extends BaseType {
    format: Format | undefined;
    setter: Setter | undefined;
    constructor(name: string = 'nil', format: Format | undefined = undefined, setter: Setter | undefined = undefined, desc: string | undefined = undefined) {
        super(name, desc);
        this.format = format;
        this.setter = setter;
        this.aux = "format";
    }
}

class Message extends BaseType {
    address: Number;
    incoming: boolean | undefined;
    signals: Signal[];
    constructor(name: string = 'nil', address: string | number | Number = -1, incoming: boolean | undefined = undefined, desc: string | undefined = undefined) {
        super(name, desc);
        this.address = new Number(address);
        this.incoming = incoming;
        this.signals = [];
        this.aux = "address";
    }
    signalCount() {
        return this.signals.length;
    }
    addSignal(sig: Signal) {
        this.signals.push(sig);
    }
    removeSignal(sig: Signal | null) {
        if (sig === null)
            return
        arrayDelete(this.signals, sig);
    }
}

class Node extends BaseType {
    gpio: GPIO[];
    messages: Message[];
    addresses: Set<Number>;
    constructor(name: string = 'nil', desc: string | undefined = undefined) {
        super(name, desc);
        this.gpio = [];
        this.messages = [];
        this.addresses = new Set();
    }
    messageCount() {
        return this.messages.length;
    }
    addMessage(msg: Message) {
        this.messages.push(msg);
    }
    removeMessage(msg: Message | null) {
        if (msg === null)
            return
        arrayDelete(this.messages, msg);
    }
}

let reset = false;

let testNode = new Node('amogus', 'hee hee hee haa');
let testMsg = new Message('YES', 0x45, false, 'amogaug');
testNode.addMessage(testMsg);
let testsig0 = new Signal('sisigi', undefined, undefined, 'chuahumuamchuma');
testMsg.addSignal(testsig0);
testNode.addresses.add(0x41);
testNode.addresses.add(0x42);
testNode.addresses.add(0x43);
testNode.addresses.add(0x44);
testNode.addresses.add(0x45);

let testNode2 = new Node('gdgd', 'hee agdhee hee haa');
let testMsg2 = new Message('ggg?', 64, true, 'agdgdagda');
testNode2.addMessage(testMsg2);
let testsig1 = new Signal('ds64', undefined, undefined, '0000 0000 0000 0000 0000 0000 0000 0000 0000');
testMsg2.addSignal(testsig1);

let testForm0 = new Format("chumga", undefined, 45, 64, 87, -32, "amgagu");
let testForm1 = new Format("SGD35", undefined, 0.4, -46, 6, 614, "HHUHUHHU");

testsig0.format = testForm0;
testsig1.format = testForm1;

let _dTypes: dType[] = [];
let _activeDType: dType | null = null;

let _formats: Format[] = [testForm0, testForm1];
let _activeFormat: Format | null = null;

let _nodes: Node[] = [testNode, testNode2];
let _activeNode: Node | null = null;
let _activeAddr: Number | null = null;
let _activeAddrInf: Message | null = null;
let _activeMsg: Message | null = null;
let _activeSig: Signal | null = null;

let _nodeRangeModMin: number | null = null;
let _nodeRangeModMax: number | null = null;

function arrayDelete(array: Array<any>, item: any) {
    if (item === null || item === undefined)
        return
    array.splice(array.findIndex(i => i === item), 1);
}


export default {
    data() {
        return {
            nodes: _nodes,
            activeNode: _activeNode,
            activeAddr: _activeAddr,
            activeAddrInf: _activeAddrInf,
            activeMsg: _activeMsg,
            activeSig: _activeSig,
            activeFormat: _activeFormat,
            activeDType: _activeDType,
            dTypes: _dTypes,
            formats: _formats,
            nodeRangeModMin: _nodeRangeModMin,
            nodeRangeModMax: _nodeRangeModMax,
            helpStr_ID: "ID must only use A-Z, 0-9, _, and no starting 0-9",
            helpStr_Addr: "Address must be either an integer or hex value (0x)",
            helpStr_Num: "Value must be a decimal number -,+,.,0-9",
            helpStr_Rng: "Min Value ≤ Max Value",
            helpStr_Form: "Select a valid format",
        }
    },
    methods: {
        checkForm(event: Event) {
            if (event === null || event.target === null)
                return

            reset = !reset;
            if (reset)
                event.target.classList.add("was-validated");
            else
                event.target.classList.remove("was-validated");
        },
        validName(name: string | undefined | null) {
            if (name === undefined || name === null)
                return false;
            const reg = RegExp('^[A-Z_]+[A-Z0-9_]*$');
            return reg.test(name);
        },
        validAddress(addr: string | number | Number | undefined | null) {
            if (addr === undefined || addr === null)
                return false;
            const reg = RegExp('^0x[A-Fa-f0-9]+|[0-9]+$');
            return reg.test(String(addr));
        },
        validNumber(num: string | number | Number | undefined | null) {
            if (num === undefined || num === null)
                return false;
            const reg = RegExp('^[+-]?([0-9]+|[0-9]+\.[0-9]+)$');
            return reg.test(String(num));
        },
        validRange(min: string | number | Number | undefined | null, max: string | number | Number | undefined | null) {
            if (min === undefined || min === null || max === undefined || max === null)
                return false;
            return Number(min) <= Number(max);
        },
        logMsg(msg: string) {
            console.log(msg);
            const form = new Intl.DateTimeFormat('en', { hour: 'numeric', minute: 'numeric', second: 'numeric' })
            logText.value = `${form.format(Date.now())} : ${msg}\n` + logText.value;
        },
        addRange() {
            if (this.activeNode === null || this.nodeRangeModMin === null || this.nodeRangeModMax === null || this.nodeRangeModMin > this.nodeRangeModMax)
                return
            for (let i = this.nodeRangeModMin; i <= this.nodeRangeModMax; i++) {
                this.activeNode.addresses.add(i);
            }
        },
        removeRange() {
            if (this.activeNode === null || this.nodeRangeModMin === null || this.nodeRangeModMax === null || this.nodeRangeModMin > this.nodeRangeModMax)
                return
            for (let i = this.nodeRangeModMin; i <= this.nodeRangeModMax; i++) {
                this.activeNode.addresses.delete(i);
            }
        },
        newDType() {
            this.activeDType = new dType(`FORMAT_${this.dTypes.length}`);
            this.dTypes.push(this.activeDType);
            this.logMsg("New format created");
        },
        deleteDType() {// TODO: add a prompt or undo function
            arrayDelete(this.dTypes, this.activeDType);
            this.activeDType = null;
            this.logMsg("Type deleted");
        },
        newFormat() {
            this.activeFormat = new Format(`FORMAT_${this.formats.length}`);
            this.formats.push(this.activeFormat);
            this.logMsg("New format created");
        },
        deleteFormat() {// TODO: add a prompt or undo function
            arrayDelete(this.formats, this.activeFormat);
            this.activeFormat = null;
            this.logMsg("Format deleted");
        },
        selectNode(node: Node | null) {
            this.activeSig = null;
            this.activeMsg = null;
            this.activeAddr = null;
            this.activeFormat = null;
            this.activeDType = null;
            this.activeNode = node;
        },
        newNode() {
            this.activeNode = new Node(`NODE_${this.nodes.length}`);
            this.selectNode(this.activeNode);
            this.nodes.push(this.activeNode);
            this.logMsg("New node created");
        },
        deleteNode() { // TODO: add a prompt or undo function
            arrayDelete(this.nodes, this.activeNode);
            this.selectNode(null);
            this.logMsg("Node deleted");
        },
        selectMessage(msg: Message | null) {
            if (this.activeNode === null)
                return
            this.activeSig = null;
            this.activeMsg = msg;
        },
        newMessage() {
            if (this.activeNode === null)
                return
            this.activeMsg = new Message(`${this.activeNode.name}_MSG_${this.activeNode.messageCount()}`.toUpperCase());
            this.selectMessage(this.activeMsg);
            this.activeNode.addMessage(this.activeMsg);
            this.logMsg("New message created");
        },
        deleteMessage() { // TODO: add a prompt or undo function
            if (this.activeNode === null)
                return
            this.activeNode.removeMessage(this.activeMsg);
            this.selectMessage(null);
            this.logMsg("Message deleted");
        },
        selectSignal(sig: Signal | null) {
            if (this.activeMsg === null)
                return
            this.activeSig = sig;
        },
        newSignal() {
            if (this.activeMsg === null)
                return
            this.activeSig = new Signal(`${this.activeMsg.name}_SIG_${this.activeMsg.signalCount()}`.toUpperCase());
            this.selectSignal(this.activeSig);
            this.activeMsg.addSignal(this.activeSig);
            this.logMsg("New signal created");
        },
        deleteSignal() { // TODO: add a prompt or undo function
            if (this.activeMsg === null)
                return
            this.activeMsg.removeSignal(this.activeSig);
            this.selectSignal(null);
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