<script lang="ts" setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

interface KonDataType {
    title: string;
    subTitle: string;
    content: string;
    link: string;
}

interface KonResponse {
    code: string;
    data: KonDataType[];
}

const konData = ref<KonDataType[]>([]);
const isLoading = ref(true);
const error = ref(false);

onMounted(async () => {
    try {
        const response = await axios.get('https://api-v2.x-x.work/web/kon/love');
        if (response.data.code === 200) {
            konData.value = response.data.data;
        } else {
            console.error('Failed to fetch love:', response.data);
            error.value = true;
        }
    } catch (err) {
        console.error('Error fetching love:', err);
        error.value = true;
    } finally {
        isLoading.value = false;
    }
});
</script>


<template>
    <div v-if="isLoading" style="text-align: center;">
        <v-skeleton-loader type="image, article"></v-skeleton-loader>
    </div>
    <div v-else-if="error">
        <p>同人作品加载失败，请加群反馈。</p>
    </div>
    <div v-else>
        <v-row>
            <v-col v-for="(item, index) in konData" :key="index" cols="12" sm="6" md="3">
                <v-card :href="item.link" target="_blank" rel="noopener" append-icon="mdi-open-in-new"
                    :subtitle="item.subTitle" :title="item.title">
                    <v-tooltip activator="parent" location="top">{{ item.content }}</v-tooltip>
                </v-card>
            </v-col>
        </v-row>
    </div>
</template>