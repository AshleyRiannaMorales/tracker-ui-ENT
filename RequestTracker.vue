<script setup>
import { ref, onMounted, watch } from 'vue';
import { FilterMatchMode } from 'primevue/api';


// For Dialog 

const visible = ref(false);
const selectedCustomer = ref(null);
const searchTerm = ref('');
const customers = ref([]);
const loading = ref(true);
const filters = ref({
    ticketID: { value: null, matchMode: FilterMatchMode.EQUALS },
});

const getSeverity = (status) => {
    switch (status) {
        case 'declined':
            return 'danger';
        case 'approved':
            return 'success';
        case 'pending':
            return 'info';
        default:
            return null;
    }
};

const fetchCustomers = () => {
    // Simulating a data fetch. Replace with actual data fetching logic.
    setTimeout(() => {
        customers.value = [
            {
                id: 1,
                ticketID: '123456',
                user: 'Amy Elsner',
                item: 'Laptop',
                quantity: 1,
                status: 'approved'
            },
            {
                id: 2,
                ticketID: '789012',
                user: 'Anna Fali',
                item: 'Monitor',
                quantity: 2,
                status: 'borrowed'
            }
            // Add more sample data as needed
        ];
        loading.value = false;
    }, 1000); // Simulated delay for fetching data
};

const openDialog = (data) => {
    selectedCustomer.value = data;
    visible.value = true;
};

onMounted(() => {
    fetchCustomers();
});

// Watch the searchTerm and update the filters accordingly
watch(searchTerm, (newTerm) => {
    if (newTerm) {
        filters.value.ticketID.value = newTerm;
    } else {
        filters.value.ticketID.value = null;
    }
});
</script>

<template>
    <!-- Dialog for More Information -->
    <Dialog v-model:visible="visible" modal :style="{ width: '30rem' }">
        <template #header>
            <div class="dialogHeader">
                <span>More Information</span>
            </div>
        </template>
        <div v-if="selectedCustomer" class="p-grid">
            <div class="info">
                <span class="infoTitle"> Ticket ID: </span> {{ selectedCustomer.ticketID }}
            </div>
            <div class="info">
                <span class="infoTitle"> User: </span> {{ selectedCustomer.user }}
            </div>
            <div class="info">
                <span class="infoTitle"> Item: </span> {{ selectedCustomer.item }}
            </div>
            <div class="info">
                <span class="infoTitle"> Quantity: </span> {{ selectedCustomer.quantity }}
            </div>
            <div class="info">
                <span class="infoTitle"> Status: </span>
                <Tag :value="selectedCustomer.status" :severity="getSeverity(selectedCustomer.status)" />
            </div>
        </div>
    </Dialog>

    <!-- Embedded code for Inter Font Style -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@100..900&display=swap" rel="stylesheet">

    <!-- DataTable -->
    <div class="page-container">
        <div class="requestTracker-container">
            <div class="container">
                <div class="header1">
                    Search for your borrowing ticket to check the status of your item request.
                </div>
                <div class="header2">
                    Enter your Ticket ID to see if your item is available, approved, borrowed, or returned.
                </div>

                <div class="searchbar-container">
                    <IconField iconPosition="right">
                        <InputIcon class="pi pi-search"></InputIcon>
                        <InputText v-model="searchTerm" placeholder="Enter your Ticket ID (*******)"
                            style="width: 100%; border-radius: 8px;" />
                    </IconField>
                </div>

                <div class="trackerTable"
                    v-if="searchTerm && customers.some(customer => customer.ticketID === searchTerm)">
                    <DataTable :value="customers" :filters="filters" dataKey="id" :loading="loading"
                        :globalFilterFields="['ticketID']">

                        <Column field="ticketID" header="Ticket ID" style="min-width: 12rem">
                            <template #body="{ data }">
                                {{ data.ticketID }}
                            </template>
                        </Column>
                        <Column field="user" header="User" style="min-width: 12rem">
                            <template #body="{ data }">
                                {{ data.user }}
                            </template>
                        </Column>
                        <Column field="item" header="Item" style="min-width: 12rem">
                            <template #body="{ data }">
                                {{ data.item }}
                            </template>
                        </Column>
                        <Column field="status" header="Status" style="min-width: 12rem">
                            <template #body="{ data }">
                                <Tag :value="data.status" :severity="getSeverity(data.status)" />
                            </template>
                        </Column>
                        <Column field="action" header="Action" style="min-width: 12rem">
                            <template #body="{ data }">
                                <Button class="actionButton" icon="pi pi-bars" @click="openDialog(data)" />
                            </template>
                        </Column>
                    </DataTable>
                </div>
            </div>
        </div>
    </div>
</template>


<style scoped>
* {
    font-family: 'Inter', sans-serif;
    font-style: 15px;
}

.page-container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    /* Full height of the viewport */
}

.container {
    text-align: center;
}

.header1 {
    color: #E81652;
    font-weight: 700;
    font-size: 23px;
    line-height: 1.25;
    margin-bottom: 10px;
}

.header2 {
    color: #959595;
    font-weight: 600;
    font-size: 15px;
    line-height: 1.25;
    margin-bottom: 15px;
}

.trackerTable {
    margin-top: 20px;
}

.actionButton {
    border-radius: 5px;
    background-color: #676767;
    border: none;
}

.dialogHeader {
    font-weight: 700;
}

.infoTitle {
    font-weight: 600;
}
</style>