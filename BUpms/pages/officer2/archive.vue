<template>
    <div class="mx-2">
        <OfficerNavbar></OfficerNavbar>
        <div class="space-y-5">
            <div class="archive-summary-wrapper  grid md:grid-cols-2 md:place-content-center border-2 rounded-lg">
                <div class="canvas-wrapper w-full md:h-96 grid place-content-center border-2 bg-slate-200">
                    <canvas id="mychart" class="h-full md:h-96"></canvas>
                </div>
                <div class="flex w-full md:h-96">
                    <div
                        class="generated-report-wrapper py-5 h-full w-full grid place-content-center md:place-content-stretch md:px-5 border-2">
                        <div class="card grid font-semibold gap-y-5 text-blue-500 tracking-wide">
                            <div
                                class="pending-project-wrapper md:text-xl text-orange-500 flex md:pt-6 place-content-center gap-5 bg-slate-200 w-full p-2 rounded-lg">
                                <div class="card-heading capitalize tracking-widest">
                                    On-review:
                                </div>
                                <div class="card-value">
                                    {{ finalPending }}
                                </div>
                            </div>
                            <div
                                class="completed-project-wrapper md:text-xl flex md:pt-6 place-content-center gap-5 bg-slate-200 w-full p-2 rounded-lg">
                                <div class="card-heading capitalize tracking-widest">
                                    Completed:
                                </div>
                                <div class="card-value ">
                                    {{ completedDataFormatted }}
                                </div>
                            </div>
                            <div
                                class="archived-project-wrapper md:text-xl text-amber-700 flex md:pt-6 place-content-center gap-5 bg-slate-200 w-full p-2 rounded-lg">
                                <div class="card-heading capitalize tracking-widest">
                                    Canceled:
                                </div>
                                <div class="card-value">
                                    {{ archivedProjectFormatted }}
                                </div>
                            </div>
                            <div
                                class="total-projects-wrapper md:text-xl flex md:pt-6 place-content-center gap-5 bg-slate-200 w-full p-2 rounded-lg">
                                <div class="card-heading capitalize tracking-widest">
                                    total:
                                </div>
                                <div class="card-value">
                                    {{ totalDataFormatted }}
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <fieldset class="border-2 border-slate-300 rounded-lg">
                <legend class="text-center font-semibold tracking-widest md:text-lg text-sm uppercase px-3">
                    History
                </legend>
                <div class="wrapper grid sm:flex text-sm md:text-base sm:justify-around">
                    <div class="filter-label ps-2 md:ps-0 md:gap-x-2 xl:text-xl">
                        <label for="" class="font-semibold">Filter by:</label>
                        <select v-model="filter" name="" id="" class="bg-slate-50 italic">
                            <option value="">None</option>
                            <option value="Completed">Completed</option>
                            <option value="Canceled">Canceled</option>
                        </select>
                    </div>
                    <div class="sort-note italic py-3 text-sm text-center md:py-0 lg:text-base">
                        History is sorted from latest to oldest date
                    </div>
                </div>
                <div class="archive-scrollbar m-1 h-screen overflow-y-scroll bg-slate-200 rounded-md">
                    <div v-for="(item, index) in archivedData" :key="index">
                        <div v-if="filter === '' && (item.isArchived || item.isCompleted)"
                            class="wrapper grid md:grid-cols-2 bg-slate-300 p-1 border-2 rounded-xl m-1 py-2 my-2 justify-between">
                            <div class="project-title text-blue-600 font-semibold uppercase text-base md:grid">
                                <div class="label-project-title pe-3 font-semibold text-gray-700 md:ps-5 md:text-lg">
                                    Title:
                                </div>
                                <div class="project-value-wrapper break-words md:indent-5 indent-2 md:text-base">
                                    {{ item.Title }}
                                </div>
                            </div>
                            <div class="wrapper md:justify-between md:flex">
                                <div class="reason-for-archiving md:grid">
                                    <label class="font-semibold md:text-lg">Remarks:</label>
                                    <div :class="item.isArchived ? 'text-red-500' : 'text-green-500'"
                                        class="font-bold indent-2 md:indent-0 md:text-lg">
                                        {{ item.isArchived ? 'Canceled' : 'Completed' }}
                                    </div>
                                </div>
                                <div class="date-created grid pe-3">
                                    <div class="wrapper">
                                        <div class="date-label-project text-gray-800 font-semibold">
                                            Created:
                                        </div>
                                        <ClientOnly>
                                            <div
                                                class="date-value-wrapper text-xs md:text-sm text-sky-600 font-bold indent-2 md:indent-0">
                                                {{ new Date(item.created).toLocaleString() }}
                                            </div>
                                        </ClientOnly>
                                    </div>
                                    <div class="wrapper">
                                        <div class="date-label-project text-gray-800 font-semibold">
                                            Updated:
                                        </div>
                                        <ClientOnly>
                                            <div
                                                class="date-value-wrapper text-xs md:text-sm text-sky-600 font-bold indent-2 md:indent-0">
                                                {{ new Date(item.updated).toLocaleString() }}
                                            </div>
                                        </ClientOnly>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div v-else-if="filter === 'Completed' && item.isCompleted"
                            class="wrapper grid md:grid-cols-2 bg-slate-300 p-1 border-2 rounded-xl m-1 py-2 my-2 justify-between">
                            <div class="project-title text-blue-600 font-semibold uppercase text-base md:grid">
                                <div class="label-project-title pe-3 font-semibold text-gray-700 md:ps-5 md:text-lg">
                                    Title:
                                </div>
                                <div class="project-value-wrapper break-words md:indent-5 indent-2 md:text-base">
                                    {{ item.Title }}
                                </div>
                            </div>
                            <div class="wrapper md:justify-between md:flex">
                                <div class="reason-for-archiving md:grid">
                                    <label class="font-semibold md:text-lg">Remarks:</label>
                                    <div :class="item.isArchived ? 'text-red-500' : 'text-green-500'"
                                        class="font-bold indent-2 md:indent-0 md:text-lg">
                                        {{ item.isArchived ? 'Canceled' : 'Completed' }}
                                    </div>
                                </div>
                                <div class="date-created grid pe-3">
                                    <div class="wrapper">
                                        <div class="date-label-project text-gray-800 font-semibold">
                                            Created:
                                        </div>
                                        <ClientOnly>
                                            <div
                                                class="date-value-wrapper text-xs md:text-sm text-sky-600 font-bold indent-2 md:indent-0">
                                                {{ new Date(item.created).toLocaleString() }}
                                            </div>
                                        </ClientOnly>
                                    </div>
                                    <div class="wrapper">
                                        <div class="date-label-project text-gray-800 font-semibold">
                                            Updated:
                                        </div>
                                        <ClientOnly>
                                            <div
                                                class="date-value-wrapper text-xs md:text-sm text-sky-600 font-bold indent-2 md:indent-0">
                                                {{ new Date(item.updated).toLocaleString() }}
                                            </div>
                                        </ClientOnly>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div v-else-if="filter === 'Canceled' && item.isArchived"
                            class="wrapper grid md:grid-cols-2 bg-slate-300 p-1 border-2 rounded-xl m-1 py-2 my-2 justify-between">
                            <div class="project-title text-blue-600 font-semibold uppercase text-base md:grid">
                                <div class="label-project-title pe-3 font-semibold text-gray-700 md:ps-5 md:text-lg">
                                    Title:
                                </div>
                                <div class="project-value-wrapper break-words md:indent-5 indent-2 md:text-base">
                                    {{ item.Title }}
                                </div>
                            </div>
                            <div class="wrapper md:justify-between md:flex">
                                <div class="reason-for-archiving md:grid">
                                    <label class="font-semibold md:text-lg">Remarks:</label>
                                    <div :class="item.isArchived ? 'text-red-500' : 'text-green-500'"
                                        class="font-bold indent-2 md:indent-0 md:text-lg">
                                        {{ item.isArchived ? 'Canceled' : 'Completed' }}
                                    </div>
                                </div>
                                <div class="date-created grid pe-3">
                                    <div class="wrapper">
                                        <div class="date-label-project text-gray-800 font-semibold">
                                            Created:
                                        </div>
                                        <ClientOnly>
                                            <div
                                                class="date-value-wrapper text-xs md:text-sm text-sky-600 font-bold indent-2 md:indent-0">
                                                {{ new Date(item.created).toLocaleString() }}
                                            </div>
                                        </ClientOnly>
                                    </div>
                                    <div class="wrapper">
                                        <div class="date-label-project text-gray-800 font-semibold">
                                            Updated:
                                        </div>
                                        <ClientOnly>
                                            <div
                                                class="date-value-wrapper text-xs md:text-sm text-sky-600 font-bold indent-2 md:indent-0">
                                                {{ new Date(item.updated).toLocaleString() }}
                                            </div>
                                        </ClientOnly>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </fieldset>
        </div>
    </div>
</template>
<script setup>
import Chart from 'chart.js/auto'
definePageMeta({
    layout: 'landing',
    middleware: ['guard', 'officer2']
})

// filter component
const filter = ref('')

// canvas main component
let canvasData;

//fetch data from api

const { $pb } = useNuxtApp()

// fetch from API 
const archivedData = await $pb.collection('Projects_tbl').getFullList({
    sort: '-updated',
})

// get total values pending projects
const pendingData = await $pb.collection('pending_project').getFullList({
});
// get total values completed projects
const completedData = await $pb.collection('completed_project').getFullList({
});
// get total values total projects
const totalData = await $pb.collection('total_projects').getFullList({
});
// get total values archived projects
const archivedProject = await $pb.collection('archived_projects').getFullList({
});

// console.log(pendingData)


// format data
const archivedProjectFormatted = archivedProject[0].archived_project
const pendingDataFormatted = pendingData[0].pending_project
const completedDataFormatted = completedData[0].completed_project
const totalDataFormatted = totalData[0].total_projects

// logic for subtracking data
const finalPending = (pendingDataFormatted - archivedProjectFormatted) - completedDataFormatted

// console.log(totalDataFormatted)
// canvas configuration
let canvasSettings = {
    type: "pie",
    data: {
        labels: ['Completed', 'On-review', 'Canceled'],
        datasets: [{
            backgroundColor: ['#0ea5e9', '#f97316', '#eab308'],
            // accepted first
            // on-review second
            data: [completedDataFormatted, finalPending, archivedProjectFormatted]
        }]
    },
}

// before update load canvas
onBeforeUpdate(() => {
    if (canvasData) {
        canvasData.destroy()
        canvasData = new Chart('mychart', canvasSettings)
        // console.log('onbeforeupdate1')
    } else {
        canvasData = new Chart('mychart', canvasSettings)
        // console.log('onbeforeupdate2')
    }
})

// before mount load canvas
onBeforeMount(() => {
    if (canvasData) {
        canvasData.destroy()
        canvasData = new Chart('mychart', canvasSettings)
        // console.log('beforemount')
    } else {
        canvasData = new Chart('mychart', canvasSettings)
        // console.log('beforemount2')
    }
})

onBeforeUnmount(() => {
    if (canvasData) {
        canvasData.destroy()

        // console.log('beforemount')
    } else {
        canvasData = new Chart('mychart', canvasSettings)
    }
})
</script>
<style scoped>
::-webkit-scrollbar {
    width: 5px;
}

::-webkit-scrollbar-thumb {
    background-color: #0369a1;
    border-radius: 20px;
}

::-webkit-scrollbar-track {
    background-color: #7dd3fc;
    border-radius: 10px;
}

::-webkit-scrollbar-thumb:hover {
    background: #0369a1;
}

.total-projects-wrapper {
    color: rgb(14, 172, 172);
}
</style>