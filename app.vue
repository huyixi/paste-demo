<template>
    <div class="min-h-screen bg-gray-50 flex items-center justify-center p-4">
        <div class="w-full max-w-md bg-white rounded-lg shadow-md p-6">
            <h1 class="text-2xl font-bold text-gray-800 mb-4">
                剪贴板粘贴演示
            </h1>

            <div class="mb-4">
                <label
                    for="paste-area"
                    class="block text-sm font-medium text-gray-700 mb-1"
                >
                    粘贴区域
                </label>
                <textarea
                    id="paste-area"
                    ref="pasteAreaRef"
                    v-model="pastedContent"
                    @paste="handlePaste"
                    placeholder="在此处粘贴内容或点击下方按钮"
                    class="w-full h-32 p-2 border border-gray-300 rounded-md focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500"
                ></textarea>
            </div>

            <div class="flex flex-col space-y-4">
                <button
                    @click="triggerPaste"
                    class="w-full py-2 px-4 bg-emerald-600 hover:bg-emerald-700 text-white font-medium rounded-md transition-colors"
                >
                    <div class="flex items-center justify-center">
                        <span class="mr-2">从剪贴板粘贴</span>
                    </div>
                </button>

                <div
                    v-if="message"
                    :class="[
                        'p-3 rounded-md text-sm',
                        messageType === 'success'
                            ? 'bg-green-100 text-green-800'
                            : 'bg-red-100 text-red-800',
                    ]"
                >
                    {{ message }}
                </div>
            </div>

            <div v-if="pastedContent" class="mt-6">
                <h2 class="text-lg font-semibold text-gray-700 mb-2">
                    粘贴的内容:
                </h2>
                <div class="p-3 bg-gray-100 rounded-md break-words">
                    {{ pastedContent }}
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref } from "vue";

const pastedContent = ref("");
const pasteAreaRef = ref(null);
const message = ref("");
const messageType = ref("success");

// 处理粘贴事件
const handlePaste = (event) => {
    try {
        // 如果是从事件中获取
        if (event) {
            event.preventDefault();
            const clipboardData = event.clipboardData || window.clipboardData;
            const pastedData = clipboardData.getData("text");
            pastedContent.value = pastedData;
            showMessage("内容已成功粘贴！", "success");
        }
    } catch (error) {
        showMessage("粘贴失败: " + error.message, "error");
        console.error("粘贴错误:", error);
    }
};

// 通过按钮触发粘贴
const triggerPaste = async () => {
    try {
        // 聚焦文本区域
        pasteAreaRef.value.focus();

        // 使用现代Clipboard API
        if (navigator.clipboard) {
            const text = await navigator.clipboard.readText();
            pastedContent.value = text;
            showMessage("内容已成功粘贴！", "success");
        } else {
            // 回退到执行粘贴命令
            document.execCommand("paste");
        }
    } catch (error) {
        showMessage("粘贴失败: " + error.message, "error");
        console.error("粘贴错误:", error);
    }
};

// 显示消息
const showMessage = (msg, type = "success") => {
    message.value = msg;
    messageType.value = type;

    // 3秒后清除消息
    setTimeout(() => {
        message.value = "";
    }, 3000);
};
</script>
