<script setup lang="ts">
import { ref } from "vue"

import { useNotification } from "naive-ui"
import {
  logArrays,
  originalLog,
  getTypeOf,
  arrayFrom,
} from "@/addons/global/helpers/customConsoleLog"
import { loadFromStorage, saveToStorage } from "@/addons/storage/storage"

let notification = useNotification()

/* 1. Load data from storage (if not exists, set true) */
let isLogsEnabled = loadFromStorage("is-logs-enabled", true)

/* 2. Decide whether logs enabled or disabled, based on storage value */
let logButtonText = Boolean(isLogsEnabled) ? ref("Hide") : ref("Show")

function changeLogsStatus() {
  isLogsEnabled = !isLogsEnabled

  saveToStorage("is-logs-enabled", isLogsEnabled)

  if (!isLogsEnabled) {
    logButtonText.value = "Show"
    notification.destroyAll()
  } else {
    logButtonText.value = "Hide"
    createLogsOnPage()
  }
}

function createLogsOnPage() {
  for (let logArray of logArrays) {
    let [firstLog, secondLog, ...rest] = logArray

    let firstLogType = getTypeOf(firstLog)
    let secondLogType = getTypeOf(secondLog)
    let restLogType = getTypeOf(rest)

    // originalLog('firstLogType', firstLogType)
    // originalLog('secondLogType', secondLogType)

    let arrayFromObject = null

    if (firstLogType == "[object Array]") {
      firstLog = firstLog.join(", ")
      firstLog = "[ " + firstLog + " ]"

      // originalLog('firstLog after transformation', firstLog);
    }

    if (secondLogType == "[object Array]") {
      secondLog = secondLog.join(", ")
      secondLog = "[ " + secondLog + " ]"

      // originalLog('secondLog after transformation', secondLog);
    }

    if (firstLogType == "[object Object]") {
      firstLog = arrayFrom(firstLog)

      firstLog.unshift("{ ")
      firstLog.push(" }")

      firstLog = firstLog.join("")
      // originalLog('firstLog after transformation', firstLog);
    }

    if (secondLogType == "[object Object]") {
      secondLog = arrayFrom(secondLog)

      secondLog.unshift("{ ")
      secondLog.push(" }")

      secondLog = secondLog.join("")
      // originalLog('secondLog after transformation', secondLog);
    }

    if (firstLog != undefined && isLogsEnabled) {
      notification.create({
        title: "" + firstLog,
        description: secondLog != undefined ? "" + secondLog : "",
        // content: rest ?  : rest '' + ,
        duration: 99999,
      })
    }
  }
}

createLogsOnPage()
</script>

<template>
  <NButton @click="changeLogsStatus">{{ logButtonText }} logs</NButton>
</template>

<style lang="scss" scoped>
@import "@scss/helpers/space.scss";
</style>
