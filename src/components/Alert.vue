<template>
    <div class="fixed inset-0 flex items-center justify-center dark:bg-gray-700 bg-gray-900 bg-opacity-75 dark:bg-opacity-75 z-50"
        v-if="isShow">
        <div class="w-64 flex flex-col items-center justify-center p-6 bg-white dark:bg-gray-800 rounded-lg shadow-lg">
            <p class="text-gray-700 dark:text-white">{{ message }}</p>
            <button @click="hideAlert"
                class="mt-4 px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600 focus:outline-none" >
                Ok
            </button>
        </div>
    </div>
</template>

<script>
export default {
    name: 'Alert',
    props: {
        isShow: Boolean,
        message: String
    },
    methods: {
        hideAlert() {
            this.$emit('update:isShow', false);
        }
    }
}
</script>