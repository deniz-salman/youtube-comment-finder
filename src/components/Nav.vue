<template>
    <nav class="bg-blue-700 dark:bg-blue-900 shadow-2xl sticky top-0 z-50">
        <div class="max-w-7xl mx-auto px-2 sm:px-6 lg:px-8">
            <div class="relative flex items-center justify-between h-16">
                <div class="flex-1 flex items-center justify-center sm:items-stretch sm:justify-start">
                    <div class="flex-shrink-0 flex items-center">
                        <img src="../assets/logo.png" alt="Youtube Logo" class="h-16" />
                    </div>
                </div>
                <div class="flex items-center">
                    <button @click="toggleDarkMode" class="text-white ml-4">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                            stroke="currentColor" class="size-6" v-if="isDarkMode">
                            <path stroke-linecap="round" stroke-linejoin="round"
                                d="M12 3v2.25m6.364.386-1.591 1.591M21 12h-2.25m-.386 6.364-1.591-1.591M12 18.75V21m-4.773-4.227-1.591 1.591M5.25 12H3m4.227-4.773L5.636 5.636M15.75 12a3.75 3.75 0 1 1-7.5 0 3.75 3.75 0 0 1 7.5 0Z" />
                        </svg>
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                            stroke="currentColor" class="size-6" v-else>
                            <path stroke-linecap="round" stroke-linejoin="round"
                                d="M21.752 15.002A9.72 9.72 0 0 1 18 15.75c-5.385 0-9.75-4.365-9.75-9.75 0-1.33.266-2.597.748-3.752A9.753 9.753 0 0 0 3 11.25C3 16.635 7.365 21 12.75 21a9.753 9.753 0 0 0 9.002-5.998Z" />
                        </svg>
                    </button>
                </div>
            </div>
        </div>
    </nav>
</template>

<script>
export default {
    name: 'Nav',
    mounted() {
        this.isDarkMode = localStorage.getItem('darkMode') === 'true';
        if (this.isDarkMode) {
            document.body.classList.add('dark');
        }
        setTimeout(() => {
            window.scrollTo({
                top: 0,
                left: 0,
                behavior: 'smooth',
            });
        }, 1);
    },
    data() {
        return {
            video: null,
            comments: [],
            isDarkMode: document.body.classList.contains('dark')
        }
    },
    methods: {
        toggleDarkMode() {
            document.body.classList.toggle('dark');
            this.isDarkMode = document.body.classList.contains('dark');
            localStorage.setItem('darkMode', this.isDarkMode);
        }
    }
}
</script>