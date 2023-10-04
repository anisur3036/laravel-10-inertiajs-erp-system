<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import ComboBox from '@/Components/CustomControl/ComboBox.vue';
import { ref } from 'vue';
import { Head } from '@inertiajs/vue3';

const fruit = ref();

function loadFruits(query, setOptions) {
    fetch("http://localhost:8000/get-options?name=" + query)
        .then(response => response.json())
        .then(results => {
            setOptions(
                results.map(fruit => {
                    return {
                        value: fruit.id,
                        label: fruit.name,
                    }
                })
            )
        });
}
</script>

<template>
    <Head title="Search Option" />
    <AuthenticatedLayout>
        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-orange-400 shadow-sm sm:rounded-lg">
                  <div class="ml-8 py-4 w-64 inline-flex items-center">
                    <ComboBox :load-options="loadFruits" v-model="fruit" />
                  </div>
                </div>
            </div>
        </div>
    </AuthenticatedLayout>
</template>
