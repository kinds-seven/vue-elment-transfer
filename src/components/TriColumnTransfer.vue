<template>
    <div class="el-transfer">
        <transfer-panel v-bind="$props" ref="leftPanel" :data="sourceData" :title="titles[0] || t('el.transfer.titles.0')"
            :default-checked="leftDefaultChecked" :placeholder="filterPlaceholder || t('el.transfer.filterPlaceholder')"
            @checked-change="onSourceCheckedChange">
            <slot name="left-footer"></slot>
        </transfer-panel>
        <div class="el-transfer__buttons">
            <div>
                <el-button type="primary" :class="['el-transfer__button', hasButtonTexts ? 'is-with-texts' : '']"
                    @click.native="addToLeft" :disabled="listTwoChecked.length === 0 && rightChecked.length === 0">
                    <i class="el-icon-arrow-left"></i>
                    <span v-if="buttonTexts[0] !== undefined">{{ buttonTexts[0] }}</span>
                </el-button>
            </div>
            <div>
                <el-button type="primary" :class="['el-transfer__button', hasButtonTexts ? 'is-with-texts' : '']"
                    @click.native="addToRight" :disabled="leftChecked.length === 0">
                    <span v-if="buttonTexts[1] !== undefined">{{ buttonTexts[1] }}</span>
                    <i class="el-icon-arrow-right"></i>
                </el-button>
            </div>
            <div>
                <el-button type="primary" :class="['el-transfer__button', hasButtonTexts ? 'is-with-texts' : '']"
                    @click.native="addToListTwo" :disabled="leftChecked.length === 0">
                    <span v-if="buttonTexts[1] !== undefined">{{ buttonTexts[2] }}</span>
                    <i class="el-icon-arrow-right"></i>
                </el-button>
            </div>
            <slot name="buttons"></slot>
        </div>
        <transfer-panel v-bind="$props" ref="rightPanel" :data="targetData" :title="titles[1] || t('el.transfer.titles.1')"
            :default-checked="rightDefaultChecked" :placeholder="filterPlaceholder || t('el.transfer.filterPlaceholder')"
            @checked-change="onTargetCheckedChange">
            <slot name="right-footer"></slot>
        </transfer-panel>
        <transfer-panel class="m-l-20" v-bind="$props" ref="rightPanel" :data="listTwoData"
            :title="titles[2] || t('el.transfer.titles.1')" :default-checked="listTwoDefaultChecked"
            :placeholder="filterPlaceholder || t('el.transfer.filterPlaceholder')"
            @checked-change="onlistTwoCheckedChange">
            <slot name="right-footer"></slot>
        </transfer-panel>
    </div>
</template>


<script>

import { Transfer } from 'element-ui'

export default {
    extends: Transfer,
    props: {
        listTwo: {
            type: Array,
            default: () => []
        },
        listTwoDefaultChecked: {
            type: Array,
            default: () => []
        },
    },
    data() {
        return {
            // 列表2选中的数据
            listTwoChecked: []
        };
    },
    computed: {
        // 列表2
        listTwoData() {
            if (this.targetOrder === 'original') {
                return this.data.filter(item => this.listTwo.indexOf(item[this.props.key]) > -1);
            } else {
                return this.listTwo.reduce((arr, cur) => {
                    const val = this.dataObj[cur];
                    if (val) {
                        arr.push(val);
                    }
                    return arr;
                }, []);
            }
        },
        sourceData() {
            return this.data.filter(item => this.value.indexOf(item[this.props.key]) === -1
                && this.listTwo.indexOf(item[this.props.key]) === -1);
        },
    },
    methods: {
        addToListTwo() {
            let currentValue = this.listTwo.slice();
            const itemsToBeMoved = [];
            const key = this.props.key;
            this.data.forEach(item => {
                const itemKey = item[key];
                if (
                    this.leftChecked.indexOf(itemKey) > -1 &&
                    this.listTwo.indexOf(itemKey) === -1
                ) {
                    itemsToBeMoved.push(itemKey);
                }
            });
            currentValue = this.targetOrder === 'unshift'
                ? itemsToBeMoved.concat(currentValue)
                : currentValue.concat(itemsToBeMoved);
            console.debug(currentValue);
            // 更新列表2
            this.$emit('update:listTwo', currentValue);
        },
        // 列表2设备选择
        onlistTwoCheckedChange(val, movedKeys) {
            this.listTwoChecked = val;
            if (movedKeys === undefined) return;
            this.$emit('right-check-change', val, movedKeys);
        },
        addToLeft() {
            // 列表1
            let currentValue = this.value.slice();
            // 列表2
            let listTwo = this.listTwo.slice();
            this.rightChecked.forEach(item => {
                const index = currentValue.indexOf(item);
                if (index > -1) {
                    currentValue.splice(index, 1);
                }
            });
            this.listTwoChecked.forEach(item => {
                const index = listTwo.indexOf(item);
                if (index > -1) {
                    listTwo.splice(index, 1);
                }
            });
            // 更新列表1
            this.$emit('input', currentValue);
            // 更新列表2
            this.$emit('update:listTwo', listTwo);
            this.$emit('change', currentValue, 'left', this.rightChecked);
        },
    },
    watch: {
        listTwo(val) {
            this.dispatch('ElFormItem', 'el.form.change', val);
        },
    }
}
</script>

<style scoped>
.m-l-20{
    margin-left: 20px;
}
</style>