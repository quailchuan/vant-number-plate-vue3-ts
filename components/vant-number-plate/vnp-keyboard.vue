<template>
  <van-action-sheet v-model:show="visible" get-container="body">
    <div class="vnp-header">
      <button type="button" class="vnp-btn-finish" @click="finish">完成</button>
    </div>

    <div class="vnp-input-box-outer">
      <vnp-input-box
        ref="inputBox"
        v-bind:value="val"
        v-on:input="val = $event"
        editable
        @activeChange="handleActiveChange"
      ></vnp-input-box>
    </div>

    <div class="vnp-keys">
      <div class="vnp-keys-row" v-for="(item, index) in list" :key="index">
        <div
          class="vnp-btn-key-wrapper"
          v-for="(data, index) in item"
          :key="index"
          :class="{
            'vnp-del-wrapper': data === 'del',
            'vnp-type-wrapper': data === 'type',
          }"
        >
          <van-button
            v-if="data === 'type'"
            class="vnp-btn-key"
            @click="handleChangeType"
          >
            <span v-if="data === 'cn'">
              中/<span class="vnp-smaller">英</span>
            </span>
            <span v-else><span class="vnp-smaller">中</span>/英</span>
          </van-button>

          <van-button
            v-else-if="data === 'del'"
            class="vnp-btn-key"
            @click="handleDel"
          >
            <svg
              viewBox="0 0 32 22"
              xmlns="http://www.w3.org/2000/svg"
              class="vnp-delete-icon"
            >
              <path
                d="M28.016 0A3.991 3.991 0 0132 3.987v14.026c0 2.2-1.787 3.987-3.98 3.987H10.382c-.509 0-.996-.206-1.374-.585L.89 13.09C.33 12.62 0 11.84 0 11.006c0-.86.325-1.62.887-2.08L9.01.585A1.936 1.936 0 0110.383 0zm0 1.947H10.368L2.24 10.28c-.224.226-.312.432-.312.73 0 .287.094.51.312.729l8.128 8.333h17.648a2.041 2.041 0 002.037-2.04V3.987c0-1.127-.915-2.04-2.037-2.04zM23.028 6a.96.96 0 01.678.292.95.95 0 01-.003 1.377l-3.342 3.348 3.326 3.333c.189.188.292.43.292.679 0 .248-.103.49-.292.679a.96.96 0 01-.678.292.959.959 0 01-.677-.292L18.99 12.36l-3.343 3.345a.96.96 0 01-.677.292.96.96 0 01-.678-.292.962.962 0 01-.292-.68c0-.248.104-.49.292-.679l3.342-3.348-3.342-3.348A.963.963 0 0114 6.971c0-.248.104-.49.292-.679A.96.96 0 0114.97 6a.96.96 0 01.677.292l3.358 3.348 3.345-3.348A.96.96 0 0123.028 6z"
                fill="currentColor"
              ></path>
            </svg>
          </van-button>

          <van-button
            v-else
            class="vnp-btn-key"
            :class="{ 'vnp-btn-empty': !data }"
            @click="handleClickKey(data)"
          >
            {{ data }}
          </van-button>
        </div>
      </div>
    </div>
  </van-action-sheet>
</template>

<script lang="ts">
import { defineComponent, reactive, ref, computed, watch } from "vue";
import Box from "./vnp-input-box.vue";

export default defineComponent({
  name: "KeyBoard",
  components: {
    "vnp-input-box": Box,
  },
  props: {
    show: {
      type: Boolean,
      default: false,
    },
    value: {
      type: String,
      default: "",
    },
  },
  setup(props, context) {
    const type = ref("cn");
    const val = ref(props.value);

    const inputBox = ref();
    const visible = computed({
      set(val) {
        context.emit("update:show", val);
      },
      get() {
        return props.show;
      },
    });

    const list = computed(() => {
      return type.value === "en" ? en : cn;
    });

    const cn = reactive([
      ["京", "津", "沪", "渝", "冀", "豫", "云", "辽", "黑", "湘"],
      ["皖", "鲁", "新", "苏", "浙", "赣", "鄂", "桂", "甘", "晋"],
      ["蒙", "陕", "吉", "闽", "贵", "粤", "青", "藏", "川", "宁"],
      ["type", "琼", "使", "领", "学", "", "", "", "del"],
    ]);
    const en = reactive([
      ["1", "2", "3", "4", "5", "6", "7", "8", "9", "0"],
      ["Q", "W", "E", "R", "T", "Y", "U", "O", "P"],
      ["A", "S", "D", "F", "G", "H", "J", "K", "L"],
      ["type", "Z", "X", "C", "V", "B", "N", "M", "del"],
    ]);
    /**
     *
     */
    const handleActiveChange = (activeIndex: number) => {
      if (activeIndex === 0) {
        type.value = "cn";
      } else {
        type.value = "en";
      }
    };
    /**
     * 键盘切换事件
     */
    const handleChangeType = () => {
      type.value = type.value === "en" ? "cn" : "en";
    };
    /**
     * 按键事件
     */
    const handleClickKey = (data: string) => {
      console.log(val.value);
      if (data.length > 0) {
        inputBox.value.setValue(data);
      }
    };
    /**
     * 退格事件
     */
    const handleDel = () => {
      inputBox.value.del();
    };
    /**
     * 完成
     */
    const finish = () => {
      context.emit("input", val.value);

      visible.value = false;
    };
    watch(
      () => props.show,
      () => {
        if (props.show) {
          val.value = props.value;
        }
      },
      { immediate: true }
    );
    return {
      inputBox,
      visible,
      type,
      cn,
      en,
      val,
      list,
      handleChangeType,
      handleActiveChange,
      handleClickKey,
      finish,
      handleDel,
    };
  },
});
</script>

<style lang="less" scoped>
.vnp-header {
  height: 40px;
  padding-top: 6px;
  position: relative;

  .vnp-btn-finish {
    position: absolute;
    right: 0;
    height: 100%;
    padding: 0 16px;
    color: #576b95;
    font-size: 14px;
    background-color: transparent;
    border: none;
    cursor: pointer;
  }
}

.vnp-input-box-outer {
  width: 82%;
  max-width: 600px;
  margin: 0 auto;
  padding: 10px;
}

.vnp-keys {
  padding: 3px;
  background: #f2f3f5;
  padding-bottom: 22px;

  .vnp-keys-row {
    display: flex;
    justify-content: center;
  }
  .vnp-btn-key-wrapper {
    flex: 0 1 calc((100% - 6px * 10) / 10);
    padding: 3px;
    box-sizing: content-box;

    &.vnp-del-wrapper,
    &.vnp-type-wrapper {
      flex: 1;
    }
    &.vnp-type-wrapper {
      .vnp-smaller {
        color: #999;
        font-size: 12px;
      }
    }

    .vnp-btn-key {
      padding: 0;
      width: 100%;
      border-radius: 4px;
    }
    .vnp-btn-empty {
      background: transparent;
      border: none;
    }
    .vnp-delete-icon {
      width: 18px;
      vertical-align: middle;
    }
  }
}
</style>
