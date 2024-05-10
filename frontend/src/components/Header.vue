<template>
    <div class="title">
        LLM-Viewer v{{ version }}

    </div>
    <div class="header_button">
        |
        <span class="bold-and-stylish">Model : </span>
        <select v-model="select_model_id">
            <option v-for="model_id in avaliable_model_ids" :value="model_id">{{ model_id }}</option>
        </select>
        <span> | </span>
        <span class="bold-and-stylish">Hardware: </span>
        <select v-model="select_hardware">
            <option v-for="hardware in avaliable_hardwares" :value="hardware">{{ hardware }}</option>
        </select>
        <span> | </span>
        <span class="bold-and-stylish">Server: </span>
        <select v-model="ip_port">
            <option value="api.llm-viewer.com:5000">api.llm-viewer.com</option>
            <option value="127.0.0.1:5000">127.0.0.1</option>
        </select>
        <span> | </span>
        <span class="hover-bold" @click="is_show_help = !is_show_help">Help</span>
        <span> | </span>
        <a href="https://github.com/hahnyuan/LLM-Viewer" target="_blank" class="hover-bold">Github</a>
    </div>
    <div v-if="is_show_help" class="float-info-window">
        <p>LLM-Viewer is an open-sourced tool to visualize the LLM model and analyze the deployment on hardware devices.
        </p>
        <p>At the center of the page, you can see the graph of the LLM model. Click the node to see the detail of the
            node.</p>
        <p>↑ At the top of the page, you can set the LLM model, hardware devices, and server. If you deploy the
            LLM-Viewer localhost, you can select the localhost server.</p>
        <p>← At the left of the page, you can see the configuration panel. You can set the inference config and
            optimization config.</p>
        <p>↙ The Network-wise Analysis result is demonstrated in the left panel.</p>
        <p>We invite you to read our paper <a class="hover-bold" href="https://arxiv.org/pdf/2402.16363.pdf"
                target="_blank">LLM Inference Unveiled: Survey and Roofline Model Insights</a>.</p>
    </div>
</template>

<script setup>
import { inject, ref, watch, onMounted } from 'vue';
import axios from 'axios';

const model_id = inject('model_id');
const hardware = inject('hardware');
const global_update_trigger = inject('global_update_trigger');
const ip_port = inject('ip_port');

const avaliable_hardwares = ref([]);
const avaliable_model_ids = ref([]);

const version = ref(llm_viewer_frontend_version)

const is_show_help = ref(false)

function update_avaliable() {
    const url = 'http://' + ip_port.value + '/get_avaliable'
    axios.get(url).then(function (response) {
        avaliable_hardwares.value = response.data.avaliable_hardwares
        avaliable_model_ids.value = response.data.avaliable_model_ids
    })
        .catch(function (error) {
            console.log("error in get_avaliable");
            console.log(error);
        });
}

onMounted(() => {
    console.log("Header mounted")
    update_avaliable()
})

var select_model_id = ref('meta-llama/Llama-2-7b-hf');
watch(select_model_id, (n) => {
    model_id.value = n
    global_update_trigger.value += 1
})

var select_hardware = ref('nvidia_V100');
watch(select_hardware, (n) => {
    hardware.value = n
    global_update_trigger.value += 1
})

watch(ip_port, (n) => {
    update_avaliable()
})
</script>

<style scoped>
.header_button button {
    font-size: 1.0rem;
    margin: 5px;
    padding: 5px;
    border-radius: 5px;
    border: none;
    background-color: #4a90e2;
    color: #ffffff;
    cursor: pointer;
    transition: background-color 0.3s;
}

.header_button button:hover {
    background-color: #357abd;
}

.header_button button:active {
    background-color: #2a5c9a;
}

.active {
    background-color: #2a5c9a;
}

.title {
    font-size: 20px;
    color: #333333;
    text-align: left;
    font-weight: bold;
    padding-right: 10px;
}

.bold-and-stylish {
    font-weight: bold;
    color: #000000;
    font-size: 16px;
    margin-right: 5px;
}

.hover-bold {
    color: black;
    transition: font-weight 0.3s;
}

.hover-bold:hover {
    font-weight: bold;
}

select {
    margin-bottom: 10px;
    margin-top: 10px;
    outline: 1;
    background: #fff;
    border: #000;
    color: #000;
    border: 1px solid crimson;
    padding: 8px;
    border-radius: 10px;
}

.float-info-window {
    position: absolute;
    top: 60px;
    left: 75%;
    transform: translateX(-50%);
    width: 40%;
    background-color: #f8f8f8;
    border: 1px solid #ddd;
    border-radius: 10px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    padding: 20px;
    z-index: 999;
}

.header_button {
    display: flex;
    align-items: center;
    gap: 10px;
    font-size: 14px;
}

div {
    margin: 5px 0;
    color: #4a4a4a;
}

a {
    color: #357abd;
    text-decoration: none;
}

a:hover {
    text-decoration: underline;
}
</style>
