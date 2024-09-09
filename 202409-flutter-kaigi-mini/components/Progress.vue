<template>
    <div class="absolute z-50 bottom-2 left-0 right-0 flex justify-center">
        <div class="w-100 relative h-1 bg-gray-200 rounded-md overflow-hidden">
            <span class="absolute h-full bg-gray-800 rounded-md transition-all duration-200" :style="style"></span>
        </div>
    </div>
</template>

<script lang="ts">
    export default {
        name: "Progress",
        props: {
            current: {
                type: Number,
                default: 0,
                required: true
            },
            total: {
                type: Number,
                default: 0,
                required: true
            },
        },
        computed: {
            style() {
                return {
                    width: `${100 / (this.total - 1)}%`,
                    left: `${100 / (this.total - 1) * (this.current - 2)}%`,
                }
            }
        }
    };
</script>
