<template>
    <div>
        <h1>ATM Money Checker</h1>
        <div class="input-box">
            <input v-model="money" type="number" placeholder="Enter money" />
            <button :disabled="isValid" @click="getMoney">Check</button>
            <p>Note: Please enter value in multiple of 10 (excluding 10 and 30).</p>
            <table>
                <tr>
                    <td>No. of 20 notes: {{ total20 }}</td>
                    <td>20 * {{ total20 }}</td>
                    <td>{{ 20 * total20 }}</td>
                </tr>
                <tr>
                    <td>No. of 50 notes: {{ total50 }}</td>
                    <td>50 * {{ total50 }}</td>
                    <td>{{ 50 * total50 }}</td>
                </tr>
                <tr>
                    <td>No. of 100 notes: {{ total100 }}</td>
                    <td>100 * {{ total100 }}</td>
                    <td>{{ 100 * total100 }}</td>
                </tr>
                <tr>
                    <td colspan="3" style="text-align: end; background: #f1f1f1">
                        {{ 20 * total20 + 50 * total50 + 100 * total100 }}
                    </td>
                </tr>
            </table>
        </div>
    </div>
</template>
<script setup lang="ts">
import { computed, ref } from 'vue'

const money = ref()
const total20 = ref(0)
const total50 = ref(0)
const total100 = ref(0)

const isValid = computed(() => {
    return money?.value == null || money?.value % 10 != 0 || money?.value == 30 || money?.value == 10
})

const loop = ref(0)

function setMoney(n: number, val100 = 0, val50 = 0, val20 = 0) {
    debugger
    let val = n
    let value100 = val100
    let value50 = val50
    let value20 = val20

    if (Math.floor(val / 100) && loop.value != 2) {
        value100 = value100 + Math.floor(val / 100)
        val = val % 100
    }

    if (Math.floor(val / 50) && loop.value != 1) {
        value50 = value50 + Math.floor(val / 50)
        val = val % 50
    }

    if (Math.floor(val / 20)) {
        value20 = value20 + Math.floor(val / 20)
        val = val % 20
    }

    if (val) {
        if (!loop.value) loop.value = 1

        if (loop.value == 1 && !value50) {
            loop.value = 2
            return setMoney(val + 100, value100 - 1, value50, value20)
        } else if (loop.value == 1 && value50) {
            return setMoney(val + 50, value100, value50 - 1, value20)
        } else if (loop.value == 2 && value50) {
            loop.value = 1
            return setMoney(val + 50, value100, value50 - 1, value20)
        }
    }

    return { value100: value100, value50: value50, value20: value20 }
}

const getMoney = () => {
    loop.value = 0
    let ans = setMoney(money.value)
    total20.value = ans?.value20
    total50.value = ans?.value50
    total100.value = ans?.value100
}
</script>
