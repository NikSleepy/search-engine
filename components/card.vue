<template >
    <div class="w-auto h-auto bg-white p-4  rounded-md m-3 ">
        <h1 class="text-xl font-bold text-blue-700">
            <a :href="`https://en.wikipedia.org/?curid=${result.pageid}`" target="_blank" rel="noopener noreferrer">{{ result.title }}</a>
        </h1>
        <p v-html="result.snippet"></p>
    </div>
</template>
<script>
export default {
    props: {
        result: {
            type: Object,
            required: true
        }
    }
}
</script>
