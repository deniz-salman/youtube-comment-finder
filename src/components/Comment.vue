<template>
    <div v-if="authorProfileImageUrl && channelIdValue && authorDisplayName && textDisplay"
        class="rounded-lg overflow-hidden flex border p-4 shadow-md">
        <img :src="authorProfileImageUrl" class="profile-avatar" />
        <div>
            <a :href="`https://www.youtube.com/channel/${channelIdValue}`" target="_blank">
                <h2 class="text-lg font-semibold">{{ authorDisplayName || 'Unknown' }}</h2>
            </a>
            <p class="text-gray-500 mb-2">{{ textDisplay || 'No comment available' }}</p>

            <span
                class="bg-gray-100 text-gray-800 text-xs font-medium inline-flex items-center px-2.5 py-0.5 rounded me-2 dark:bg-gray-700 dark:text-gray-400 border border-gray-500">
                👍
                {{ parseInt(likeCount || 0).toLocaleString() }}
            </span>
            <span
                class="bg-gray-100 text-gray-800 text-xs font-medium inline-flex items-center px-2.5 py-0.5 rounded me-2 dark:bg-gray-700 dark:text-gray-400 border border-gray-500">
                💬
                {{ parseInt(totalReplyCount || 0).toLocaleString() }}
            </span>
            <span
                class="bg-gray-100 text-gray-800 text-xs font-medium inline-flex items-center px-2.5 py-0.5 rounded me-2 dark:bg-gray-700 dark:text-gray-400 border border-gray-500">
                🕒
                {{
                    formatDateTime(publishedAt)
                }}
            </span>
            <br>
            <div class="mt-2">
                <a class="text-blue-500 hover:text-blue-700 text-sm"
                    :href="`https://www.youtube.com/watch?v=${videoId}&lc=${commentId}`" target="_blank">
                    Show comment
                </a>
            </div>

        </div>
    </div>
</template>

<script>

export default {
    name: 'Comment',
    props: {
        authorProfileImageUrl: String,
        channelIdValue: String,
        authorDisplayName: String,
        textDisplay: String,
        likeCount: String,
        totalReplyCount: String,
        publishedAt: String,
        videoId: String,
        commentId: String
    },
    methods: {
        formatDateTime(publishedAt) {
            const date = new Date(publishedAt);
            const localDate = date.toLocaleDateString();
            const localTime = date.toLocaleTimeString([], {
                hour: '2-digit',
                minute: '2-digit'
            });
            return `${localDate} ${localTime}`;
        }
    }
}
</script>
