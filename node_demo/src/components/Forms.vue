<template>
    <div>
        <h4 class="display-5">{{ this.formType }} Symbol </h4>
        <va-form style="width: 500px;"
            tag="form"
            @submit="handleSubmit"
        >
            <va-input 
                class="my-2" 
                v-model="formData.name" 
                label="name"
                placeholder="Enter Symbol Name"
            />
            <va-input 
                class="my-2" 
                v-model="formData.description" 
                label="description"
                placeholder="Enter Symbol Description"
            />
            <va-input 
                class="my-2" 
                v-model="formData.currency" 
                label="currency"
                placeholder="Enter Symbol Currency"
            />
            <va-select style="width:500px;" 
                class="my-4" 
                v-model="formData.type" 
                label="type"
                :options="['Forex','Commodity','Index','Test','Stock']"
            />
            <va-input 
                class="my-2" 
                v-model="formData.digits" 
                label="digits"
                placeholder="Enter Symbol Digits"
            />
            <va-select style="width:500px;" 
                class="my-4" 
                v-model="formData.trade" 
                label="trade"
                :options="['Full','Close Only','No']"
            />
            <va-input 
                class="my-2" 
                v-model="formData.swap_long" 
                label="swap long"
                placeholder="Enter Symbol Swap Long"
            />
            <va-input 
                class="my-2" 
                v-model="formData.swap_short" 
                label="swap short"
                placeholder="Enter Symbol Swap Short"
            />
            <va-input 
                class="my-2" 
                v-model="formData.contract_size" 
                label="contract size"
                placeholder="Enter Symbol Contract Size"
            />
            <va-button type="submit" class="mt-2" @click="handleSubmit">SAVE</va-button>
        </va-form>
    </div>
</template>
<script>
import { defineComponent } from 'vue';
import axios from 'axios';

export default defineComponent({
    name: "EditForm",
    props: ['itemData','formType'],
    data() {
        return {
            formData: {
                "id": null,
                "server_id": null,
                "name": "",
                "description": "",
                "currency": "",
                "type": "",
                "digits": null,
                "trade": "",
                "swap_long": null,
                "swap_short": null,
                "contract_size": null
            }
        }
    },
    created(){
        console.log(this.formType);
        if(this.itemData != undefined){
            this.formData = this.itemData;
        }
    },
    methods: {
        async handleSubmit(e){
            e.preventDefault();
            const data = JSON.parse(JSON.stringify(this.formData));
            console.log(data);
            if(this.formType === "edit"){
                const res = await axios.post(`http://localhost:3000/symbols/change/${data.id}`,data);
                console.log(res.data);
            }else if(this.formType === "add"){
                const res = await axios.post("http://localhost:3000/symbols/add",data);
                console.log(res.data);
            }
            this.$parent.reload();
        }
    },
})
</script>
<style scoped>
    
</style>