<template>
    <el-dialog v-model="dialogVisible" title="参数配置" width="600px">
        <el-form ref="formRef" :model="form" :rules="rules" label-width="140px">
            <el-collapse v-model="activeNames">
                <el-collapse-item title="TG" name="1">
                    <el-form-item label="TG-token" prop="tg_token">
                        <el-input v-model="form.tg_token" placeholder="请输入 Telegram Bot Token" id="tg-token-input" />
                    </el-form-item>
                    <el-form-item label="TG-userid" prop="tg_userid">
                        <el-input v-model="form.tg_userid" placeholder="请输入 Telegram User ID" id="tg-userid-input" />
                    </el-form-item>
                </el-collapse-item>
                <el-collapse-item title="微信" name="2">
                    <el-form-item label="WX-API" prop="wx_api">
                        <el-input v-model="form.wx_api" placeholder="请输入 WX-API" id="wx-api-input" />
                    </el-form-item>
                    <el-form-item label="WX-token" prop="wx_token">
                        <el-input v-model="form.wx_token" placeholder="请输入 WX-token" id="wx-token-input" />
                    </el-form-item>
                </el-collapse-item>
            </el-collapse>
            <el-form-item label="剩余多少天告警" prop="days">
                <el-input-number v-model="form.days" :min="1" :max="365" id="days-input" />
            </el-form-item>
        </el-form>
        <template #footer>
            <span class="dialog-footer">
                <el-button @click="dialogVisible = false">取消</el-button>
                <el-button type="primary" @click="handleSubmit">提交</el-button>
            </span>
        </template>
    </el-dialog>
</template>

<script setup lang="ts">
import { ref, defineProps, defineEmits, watch } from 'vue'
import type { FormInstance, FormRules } from 'element-plus'

interface AlertConfigForm {
    tg_token: string
    tg_userid: string
    wx_api: string
    wx_token: string
    days: number
}

const props = defineProps<{
    visible: boolean
    config?: AlertConfigForm
}>()

const emit = defineEmits(['update:visible', 'submit'])

const dialogVisible = ref(props.visible)
const formRef = ref<FormInstance>()
const activeNames = ref(['1', '2'])

const form = ref<AlertConfigForm>({
    tg_token: '',
    tg_userid: '',
    wx_api: '',
    wx_token: '',
    days: 30
})

const rules = {
    days: [
        { required: true, message: '请输入告警天数', trigger: 'change' }
    ]
} satisfies FormRules

watch(() => props.visible, (newVal) => {
    dialogVisible.value = newVal
})

watch(dialogVisible, (newVal) => {
    emit('update:visible', newVal)
})

watch(() => props.config, (newVal) => {
    if (newVal) {
        form.value = { ...newVal }
    }
}, { immediate: true })

const handleSubmit = async () => {
    if (!formRef.value) return

    await formRef.value.validate((valid) => {
        if (valid) {
            emit('submit', form.value)
            dialogVisible.value = false
        }
    })
}
</script>

<style scoped>
.dialog-footer {
    display: flex;
    justify-content: flex-end;
    gap: 10px;
}
</style>