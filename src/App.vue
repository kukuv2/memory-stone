<template>
    <div id="app">
        <div class="pageIndicate">
            {{this.columnNumber + 1}} / {{this.columnDatas.length -1}}:column
            <br>
            {{this.rowNumber + 1}} / {{currentColumnRowLength - 1}}:row
        </div>
        <flexbox :gutter="0"
                 class="fullHeight overHidden">
            <template v-for="(column,columnIndex) in renderColumnData">
                <flexbox-item ref="flexboxItem"
                              class="columnItem fullHeight"
                              :span="getColumnSpan(columnIndex)">
                    <flexbox :gutter="0"
                             :grid="rowGrid"
                             class="overHidden fullHeight"
                             orient="vertical">
                        <template v-if="columnIndex === transformColumn">
                            <template v-if="rowNumber === 0">
                                <flexbox-item class="rowItem"
                                              :span="3">
                                </flexbox-item>
                            </template>
                            <template v-for="(row,rowIndex) in renderRowData">
                                <flexbox-item class="rowItem"
                                              :class="getRowCenterClass(rowIndex,columnIndex)"
                                              :span="3">
                                    <template v-if="row">
                                        <div>
                                            {{row.sentence}}
                                        </div>
                                    </template>
                                </flexbox-item>
                            </template>
                        </template>
                        <template v-else>
                            <template v-for="(row,rowIndex) in column">
                                <flexbox-item class="rowItem"
                                              :span="1">
                                    <div>
                                        {{row.sentence}}
                                    </div>
                                </flexbox-item>
                            </template>
                        </template>
                    </flexbox>
                </flexbox-item>
            </template>
        </flexbox>
    </div>
</template>

<script>
    import {Flexbox, FlexboxItem} from './components/flexbox'
    import _ from 'lodash'
    import key from 'keymaster'

    export default {
        name: 'App',
        components: {
            Flexbox,
            FlexboxItem
        },
        created: function () {
            var me = this;
            key('up', function () {
                if (me.rowNumber < me.currentColumnRowLength -2) {
                    me.rowNumber++;
                }
            });
            key('down', function () {
                if (me.rowNumber !== 0) {
                    me.rowNumber--;
                }
            });
            key('left', function () {
                if (me.columnNumber !== 0) {
                    me.columnNumber--;
                    me.rowNumber = 0
                }
            });
            key('right', function () {
                if(me.columnNumber < me.columnDatas.length - 2){
                    me.columnNumber++;
                    me.rowNumber = 0
                }
            });
        },
        computed: {
            currentColumnRowLength:function () {
                var number = this.columnNumber+1
                return this.columnDatas[number].length
            },
            pageColumn: function () {
                return 12 / this.columnSpan;
            },
            transformColumn: function () {
                return Math.floor(this.pageColumn / 2)
            },
            transformRow: function () {
                return Math.floor(this.columnRow / 2)
            },
            renderColumnData: function () {
                return this.columnDatas.slice(this.columnNumber, this.columnNumber + this.pageColumn)
            },
            renderRowData:function () {
                var centerColumnData = this.renderColumnData[this.transformColumn]
                if(this.rowNumber === 0){
                    return centerColumnData.slice(this.rowNumber,this.rowNumber + 2)
                }
                return centerColumnData.slice(this.rowNumber,this.rowNumber + 3)
            }
        },
        methods: {
            getColumnSpan: function (columnIndex) {
                if (columnIndex === this.transformColumn) {
                    return 6
                } else {
                    return 3
                }
            },
            getRowSpan: function (rowIndex, columnIndex) {
                if (columnIndex === this.transformColumn) {
                    return 3
                } else {
                    return 1
                }
            },
            getRowCenterClass: function (rowIndex) {
                if(this.rowNumber === 0 && rowIndex === 0){
                    return {
                        'rowCenterClass': true
                    }
                }else if (this.rowNumber !== 0 && rowIndex === 1) {
                    return {
                        'rowCenterClass': true
                    }
                } else {
                    return {}
                }
            }
        },
        data: function () {
            var wordData = _.range(1, 50).map(function (item, index) {
                return {
                    keyword: item,
                    sentence: `just fuck ${item}`
                }
            });
            var columnRow = 9;
            var columnDatas = _.chunk(wordData, columnRow);
            columnDatas.unshift([]);
            return {
                columnDatas,
                columnNumber: 0,
                rowNumber: 0,
                columnSpan: 4,
                rowGrid: columnRow,
                columnRow
            }

        }
    }
</script>

<style lang="less">
    @import (reference) "./styles/index";
    @import "./styles/commonClass";
    @import "./styles/reset";

    html {
        .fullHeight;
    }

    body {
        display: flex;
        align-items: center;
        justify-content: center;
        .fullHeight;
    }

    #app {
        font-family: Source Sans Pro, Helvetica, sans-serif;
        .fullHeight;
        color: @theme-color;
        .fullWight;
        .pageIndicate {
            position: fixed;
            right: 0px;
            top: 0px;
        }
        .columnItem {
            .fullHeight;
            .rowCenterClass {
                background-color: silver;
            }
            .rowItem {
                text-align: center;
                flex-grow: 1;
            }
        }
    }

</style>
