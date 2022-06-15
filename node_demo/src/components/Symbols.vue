<template>
    <h1 class="display-1">Symbols Management</h1>
    <div class="row">
        <va-input 
            class="flex mb-2 md6"
            placeholder="Filter Here!"
            v-model="filter"
        />
    </div>
    <va-data-table
        :items="symbols"
        :columns="columns"
        :selectable="selectable"
        v-model="selectedItems"
        :select-mode="selectMode"
        :selected-color="selectedColor"
        :filter="filter"
        @selectionChange="selectedItemsEmitted = $event.currentSelectedItems"
    />
    <va-divider></va-divider>
    <va-alert class="mt-3" border="left">
        <span>
            <va-button outline color="info" @click="showForm = !showForm">Add Symbol</va-button>
        </span>
        <span>
            Selected symbol:
            <va-chip
                class="ml-2"
                :key="item.id"
                v-for="item in selectedItemsEmitted"
            >
            {{ item.id }}
            </va-chip>
        </span>
        <span>
            <va-button outline color="warning"  @click="showEdit = !showEdit">Edit Symbol</va-button>
        </span>
        <span>
            <va-button outline color="danger" @click="removeSymbol">Delete Symbol</va-button>
        </span>
    </va-alert>
    <va-divider></va-divider>
    <div style="display: flex; justify-content:center;">
        <EditForm v-if="showForm" formType="add" @reloadTable="reload"/>
    </div>
    <div style="display: flex; justify-content:center;">
        <EditForm v-if="showEdit" :itemData="selectedItems[0]" formType="edit"/>
    </div>
</template>
<script>
import axios from 'axios';
import { defineComponent } from 'vue';
import EditForm from './Forms.vue';

export default defineComponent({
    name: "SymbolsTable",
    components: {
        EditForm,
    },
    data() {
        let symbols = []
        const columns = [
            { key: 'id', sortable: true},
            { key: 'server_id', sortable: true},
            { key: 'name', sortable: true},
            { key: 'description', sortable: true},
            { key: 'currency', sortable: true},
            { key: 'type', sortable: true},
            { key: 'digits', sortable: true},
            { key: 'trade', sortable: true},
            { key: 'swap_long', sortable: true},
            { key: 'swap_short', sortable: true},
            { key: 'contract_size', sortable: true}
        ]

        return {
            symbols,
            columns,
            selectedItems: [],
            selectedItemsEmitted: [],
            selectable: true,
            selectMode: 'single',
            selectedColor: '#888888',
            filter:'',
            showForm: false,
            showEdit: false,
        }
    },
    async created() {
        try {
            const res = await axios.get('http://localhost:3000/symbols');
            this.symbols = this.converter(res.data);
            console.log(res);
        }catch(e){
            console.log(e);
        }
    }, 
    watch: {
        selectable (value) {
            if(!value){
                this.selectedItems = [];
            }
        }
    },
    methods: {
        unselectItem(item) {
            const index = this.selectedItems.indexOf(item);
            this.selectedItems = [
                ...this.selectedItems.slice(0, index),
                ...this.selectedItems.slice(index + 1)
            ]
        },
        async removeSymbol() {
            try{
                const id = this.selectedItems[0].id;
                const res = await axios.post(`http://localhost:3000/symbols/delete/${id}`);
                this.symbols = res.data;
            }catch(e){
                console.log(e);
            }
        },
        converter(data){
            let temp = data;
           //console.log(temp);
            temp.forEach((item) => {
                if(item.type == 1){
                    item.type = "Forex";
                }else if(item.type == 2){
                    item.type = "Commodity";
                }else if(item.type == 3){
                    item.type = "Index";
                }else if(item.type == 4){
                    item.type = "Test";
                }else if(item.type == 5){
                    item.type = "Stocks";
                }else{
                    item.type = "Unknown";
                }

                if(item.trade == 0) {
                    item.trade = "No";
                }else if(item.trade == 1){
                    item.trade = "Close Only";
                }else if(item.trade == 2){
                    item.trade = "Full";
                }else{
                    item.trade = "Unknown";
                }
            })
            return temp;
        },
        async reload(){
            try {
                const res = await axios.get('http://localhost:3000/symbols');
                this.symbols = this.converter(res.data);
                console.log(res);
            }catch(e){
                console.log(e);
            }
        }

    },
})
</script>
<style scoped>
    .row {
        padding-left: 20px;
    }
    .display-1 {
        margin-bottom: 20px;
    }
    span + span {
        margin-left: 15px;
    }


</style>