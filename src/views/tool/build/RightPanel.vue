<template>
  <div class="right-board">
    <el-tabs v-model="currentTab" class="center-tabs">
      <el-tab-pane label="组件属性" name="field" />
      <el-tab-pane label="表单属性" name="form" />
    </el-tabs>
    <div class="field-box">
      <a
        class="document-link"
        target="_blank"
        :href="documentLink"
        title="查看组件文档"
      >
        <i class="el-icon-link" />
      </a>
      <el-scrollbar class="right-scrollbar">
        <!-- 组件属性 -->
        <el-form
          v-show="currentTab === 'field' && showField"
          size="small"
          label-width="90px"
        >
          <el-form-item v-if="activeData_.changeTag" label="组件类型">
            <el-select
              v-model="activeData_.tagIcon"
              placeholder="请选择组件类型"
              :style="{ width: '100%' }"
              @change="tagChange"
            >
              <el-option-group
                v-for="group in tagList"
                :key="group.label"
                :label="group.label"
              >
                <el-option
                  v-for="item in group.options"
                  :key="item.label"
                  :label="item.label"
                  :value="item.tagIcon"
                >
                  <svg-icon class="node-icon" :icon-class="item.tagIcon" />
                  <span> {{ item.label }}</span>
                </el-option>
              </el-option-group>
            </el-select>
          </el-form-item>
          <el-form-item v-if="activeData_.vModel !== undefined" label="字段名">
            <el-input
              v-model="activeData_.vModel"
              placeholder="请输入字段名（v-model）"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_.componentName !== undefined"
            label="组件名"
          >
            {{ activeData_.componentName }}
          </el-form-item>
          <el-form-item v-if="activeData_.label !== undefined" label="标题">
            <el-input v-model="activeData_.label" placeholder="请输入标题" />
          </el-form-item>
          <el-form-item
            v-if="activeData_.placeholder !== undefined"
            label="占位提示"
          >
            <el-input
              v-model="activeData_.placeholder"
              placeholder="请输入占位提示"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_['start-placeholder'] !== undefined"
            label="开始占位"
          >
            <el-input
              v-model="activeData_['start-placeholder']"
              placeholder="请输入占位提示"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_['end-placeholder'] !== undefined"
            label="结束占位"
          >
            <el-input
              v-model="activeData_['end-placeholder']"
              placeholder="请输入占位提示"
            />
          </el-form-item>
          <el-form-item v-if="activeData_.span !== undefined" label="表单栅格">
            <el-slider
              v-model="activeData_.span"
              :max="24"
              :min="1"
              :marks="{ 12: '' }"
              @change="spanChange"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_.layout === 'rowFormItem'"
            label="栅格间隔"
          >
            <el-input-number
              v-model="activeData_.gutter"
              :min="0"
              placeholder="栅格间隔"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_.layout === 'rowFormItem'"
            label="布局模式"
          >
            <el-radio-group v-model="activeData_.type">
              <el-radio-button label="default" />
              <el-radio-button label="flex" />
            </el-radio-group>
          </el-form-item>
          <el-form-item
            v-if="
              activeData_.justify !== undefined && activeData_.type === 'flex'
            "
            label="水平排列"
          >
            <el-select
              v-model="activeData_.justify"
              placeholder="请选择水平排列"
              :style="{ width: '100%' }"
            >
              <el-option
                v-for="(item, index) in justifyOptions"
                :key="index"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
          </el-form-item>
          <el-form-item
            v-if="
              activeData_.align !== undefined && activeData_.type === 'flex'
            "
            label="垂直排列"
          >
            <el-radio-group v-model="activeData_.align">
              <el-radio-button label="top" />
              <el-radio-button label="middle" />
              <el-radio-button label="bottom" />
            </el-radio-group>
          </el-form-item>
          <el-form-item
            v-if="activeData_.labelWidth !== undefined"
            label="标签宽度"
          >
            <el-input
              v-model.number="activeData_.labelWidth"
              type="number"
              placeholder="请输入标签宽度"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_.style && activeData_.style.width !== undefined"
            label="组件宽度"
          >
            <el-input
              v-model="activeData_.style.width"
              placeholder="请输入组件宽度"
              clearable
            />
          </el-form-item>
          <el-form-item v-if="activeData_.vModel !== undefined" label="默认值">
            <el-input
              :value="setDefaultValue(activeData_.defaultValue)"
              placeholder="请输入默认值"
              @input="onDefaultValueInput"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_.tag === 'el-checkbox-group'"
            label="至少应选"
          >
            <el-input-number
              :value="activeData_.min"
              :min="0"
              placeholder="至少应选"
              @input="$set(activeData_, 'min', $event ? $event : undefined)"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_.tag === 'el-checkbox-group'"
            label="最多可选"
          >
            <el-input-number
              :value="activeData_.max"
              :min="0"
              placeholder="最多可选"
              @input="$set(activeData_, 'max', $event ? $event : undefined)"
            />
          </el-form-item>
          <el-form-item v-if="activeData_.prepend !== undefined" label="前缀">
            <el-input v-model="activeData_.prepend" placeholder="请输入前缀" />
          </el-form-item>
          <el-form-item v-if="activeData_.append !== undefined" label="后缀">
            <el-input v-model="activeData_.append" placeholder="请输入后缀" />
          </el-form-item>
          <el-form-item
            v-if="activeData_['prefix-icon'] !== undefined"
            label="前图标"
          >
            <el-input
              v-model="activeData_['prefix-icon']"
              placeholder="请输入前图标名称"
            >
              <el-button
                slot="append"
                icon="el-icon-thumb"
                @click="openIconsDialog('prefix-icon')"
              >
                选择
              </el-button>
            </el-input>
          </el-form-item>
          <el-form-item
            v-if="activeData_['suffix-icon'] !== undefined"
            label="后图标"
          >
            <el-input
              v-model="activeData_['suffix-icon']"
              placeholder="请输入后图标名称"
            >
              <el-button
                slot="append"
                icon="el-icon-thumb"
                @click="openIconsDialog('suffix-icon')"
              >
                选择
              </el-button>
            </el-input>
          </el-form-item>
          <el-form-item
            v-if="activeData_.tag === 'el-cascader'"
            label="选项分隔符"
          >
            <el-input
              v-model="activeData_.separator"
              placeholder="请输入选项分隔符"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_.autosize !== undefined"
            label="最小行数"
          >
            <el-input-number
              v-model="activeData_.autosize.minRows"
              :min="1"
              placeholder="最小行数"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_.autosize !== undefined"
            label="最大行数"
          >
            <el-input-number
              v-model="activeData_.autosize.maxRows"
              :min="1"
              placeholder="最大行数"
            />
          </el-form-item>
          <el-form-item v-if="activeData_.min !== undefined" label="最小值">
            <el-input-number v-model="activeData_.min" placeholder="最小值" />
          </el-form-item>
          <el-form-item v-if="activeData_.max !== undefined" label="最大值">
            <el-input-number v-model="activeData_.max" placeholder="最大值" />
          </el-form-item>
          <el-form-item v-if="activeData_.step !== undefined" label="步长">
            <el-input-number v-model="activeData_.step" placeholder="步数" />
          </el-form-item>
          <el-form-item
            v-if="activeData_.tag === 'el-input-number'"
            label="精度"
          >
            <el-input-number
              v-model="activeData_.precision"
              :min="0"
              placeholder="精度"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_.tag === 'el-input-number'"
            label="按钮位置"
          >
            <el-radio-group v-model="activeData_['controls-position']">
              <el-radio-button label=""> 默认 </el-radio-button>
              <el-radio-button label="right"> 右侧 </el-radio-button>
            </el-radio-group>
          </el-form-item>
          <el-form-item
            v-if="activeData_.maxlength !== undefined"
            label="最多输入"
          >
            <el-input
              v-model="activeData_.maxlength"
              placeholder="请输入字符长度"
            >
              <template slot="append"> 个字符 </template>
            </el-input>
          </el-form-item>
          <el-form-item
            v-if="activeData_['active-text'] !== undefined"
            label="开启提示"
          >
            <el-input
              v-model="activeData_['active-text']"
              placeholder="请输入开启提示"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_['inactive-text'] !== undefined"
            label="关闭提示"
          >
            <el-input
              v-model="activeData_['inactive-text']"
              placeholder="请输入关闭提示"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_['active-value'] !== undefined"
            label="开启值"
          >
            <el-input
              :value="setDefaultValue(activeData_['active-value'])"
              placeholder="请输入开启值"
              @input="onSwitchValueInput($event, 'active-value')"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_['inactive-value'] !== undefined"
            label="关闭值"
          >
            <el-input
              :value="setDefaultValue(activeData_['inactive-value'])"
              placeholder="请输入关闭值"
              @input="onSwitchValueInput($event, 'inactive-value')"
            />
          </el-form-item>
          <el-form-item
            v-if="
              activeData_.type !== undefined &&
              'el-date-picker' === activeData_.tag
            "
            label="时间类型"
          >
            <el-select
              v-model="activeData_.type"
              placeholder="请选择时间类型"
              :style="{ width: '100%' }"
              @change="dateTypeChange"
            >
              <el-option
                v-for="(item, index) in dateOptions"
                :key="index"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
          </el-form-item>
          <el-form-item
            v-if="activeData_.name !== undefined"
            label="文件字段名"
          >
            <el-input
              v-model="activeData_.name"
              placeholder="请输入上传文件字段名"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_.accept !== undefined"
            label="文件类型"
          >
            <el-select
              v-model="activeData_.accept"
              placeholder="请选择文件类型"
              :style="{ width: '100%' }"
              clearable
            >
              <el-option label="图片" value="image/*" />
              <el-option label="视频" value="video/*" />
              <el-option label="音频" value="audio/*" />
              <el-option label="excel" value=".xls,.xlsx" />
              <el-option label="word" value=".doc,.docx" />
              <el-option label="pdf" value=".pdf" />
              <el-option label="txt" value=".txt" />
            </el-select>
          </el-form-item>
          <el-form-item
            v-if="activeData_.fileSize !== undefined"
            label="文件大小"
          >
            <el-input
              v-model.number="activeData_.fileSize"
              placeholder="请输入文件大小"
            >
              <el-select
                slot="append"
                v-model="activeData_.sizeUnit"
                :style="{ width: '66px' }"
              >
                <el-option label="KB" value="KB" />
                <el-option label="MB" value="MB" />
                <el-option label="GB" value="GB" />
              </el-select>
            </el-input>
          </el-form-item>
          <el-form-item
            v-if="activeData_.action !== undefined"
            label="上传地址"
          >
            <el-input
              v-model="activeData_.action"
              placeholder="请输入上传地址"
              clearable
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_['list-type'] !== undefined"
            label="列表类型"
          >
            <el-radio-group v-model="activeData_['list-type']" size="small">
              <el-radio-button label="text"> text </el-radio-button>
              <el-radio-button label="picture"> picture </el-radio-button>
              <el-radio-button label="picture-card">
                picture-card
              </el-radio-button>
            </el-radio-group>
          </el-form-item>
          <el-form-item
            v-if="activeData_.buttonText !== undefined"
            v-show="'picture-card' !== activeData_['list-type']"
            label="按钮文字"
          >
            <el-input
              v-model="activeData_.buttonText"
              placeholder="请输入按钮文字"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_['range-separator'] !== undefined"
            label="分隔符"
          >
            <el-input
              v-model="activeData_['range-separator']"
              placeholder="请输入分隔符"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_['picker-options'] !== undefined"
            label="时间段"
          >
            <el-input
              v-model="activeData_['picker-options'].selectableRange"
              placeholder="请输入时间段"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_.format !== undefined"
            label="时间格式"
          >
            <el-input
              :value="activeData_.format"
              placeholder="请输入时间格式"
              @input="setTimeValue($event)"
            />
          </el-form-item>
          <template
            v-if="
              ['el-checkbox-group', 'el-radio-group', 'el-select'].indexOf(
                activeData_.tag
              ) > -1
            "
          >
            <el-divider>选项</el-divider>
            <draggable
              :list="activeData_.options"
              :animation="340"
              group="selectItem"
              handle=".option-drag"
            >
              <div
                v-for="(item, index) in activeData_.options"
                :key="index"
                class="select-item"
              >
                <div class="select-line-icon option-drag">
                  <i class="el-icon-s-operation" />
                </div>
                <el-input
                  v-model="item.label"
                  placeholder="选项名"
                  size="small"
                />
                <el-input
                  placeholder="选项值"
                  size="small"
                  :value="item.value"
                  @input="setOptionValue(item, $event)"
                />
                <div
                  class="close-btn select-line-icon"
                  @click="activeData_.options.splice(index, 1)"
                >
                  <i class="el-icon-remove-outline" />
                </div>
              </div>
            </draggable>
            <div style="margin-left: 20px">
              <el-button
                style="padding-bottom: 0"
                icon="el-icon-circle-plus-outline"
                type="text"
                @click="addSelectItem"
              >
                添加选项
              </el-button>
            </div>
            <el-divider />
          </template>

          <template v-if="['el-cascader'].indexOf(activeData_.tag) > -1">
            <el-divider>选项</el-divider>
            <el-form-item label="数据类型">
              <el-radio-group v-model="activeData_.dataType" size="small">
                <el-radio-button label="dynamic"> 动态数据 </el-radio-button>
                <el-radio-button label="static"> 静态数据 </el-radio-button>
              </el-radio-group>
            </el-form-item>

            <template v-if="activeData_.dataType === 'dynamic'">
              <el-form-item label="标签键名">
                <el-input
                  v-model="activeData_.labelKey"
                  placeholder="请输入标签键名"
                />
              </el-form-item>
              <el-form-item label="值键名">
                <el-input
                  v-model="activeData_.valueKey"
                  placeholder="请输入值键名"
                />
              </el-form-item>
              <el-form-item label="子级键名">
                <el-input
                  v-model="activeData_.childrenKey"
                  placeholder="请输入子级键名"
                />
              </el-form-item>
            </template>

            <el-tree
              v-if="activeData_.dataType === 'static'"
              draggable
              :data="activeData_.options"
              node-key="id"
              :expand-on-click-node="false"
              :render-content="renderContent"
            />
            <div
              v-if="activeData_.dataType === 'static'"
              style="margin-left: 20px"
            >
              <el-button
                style="padding-bottom: 0"
                icon="el-icon-circle-plus-outline"
                type="text"
                @click="addTreeItem"
              >
                添加父级
              </el-button>
            </div>
            <el-divider />
          </template>

          <el-form-item
            v-if="activeData_.optionType !== undefined"
            label="选项样式"
          >
            <el-radio-group v-model="activeData_.optionType">
              <el-radio-button label="default"> 默认 </el-radio-button>
              <el-radio-button label="button"> 按钮 </el-radio-button>
            </el-radio-group>
          </el-form-item>
          <el-form-item
            v-if="activeData_['active-color'] !== undefined"
            label="开启颜色"
          >
            <el-color-picker v-model="activeData_['active-color']" />
          </el-form-item>
          <el-form-item
            v-if="activeData_['inactive-color'] !== undefined"
            label="关闭颜色"
          >
            <el-color-picker v-model="activeData_['inactive-color']" />
          </el-form-item>

          <el-form-item
            v-if="activeData_['allow-half'] !== undefined"
            label="允许半选"
          >
            <el-switch v-model="activeData_['allow-half']" />
          </el-form-item>
          <el-form-item
            v-if="activeData_['show-text'] !== undefined"
            label="辅助文字"
          >
            <el-switch
              v-model="activeData_['show-text']"
              @change="rateTextChange"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_['show-score'] !== undefined"
            label="显示分数"
          >
            <el-switch
              v-model="activeData_['show-score']"
              @change="rateScoreChange"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_['show-stops'] !== undefined"
            label="显示间断点"
          >
            <el-switch v-model="activeData_['show-stops']" />
          </el-form-item>
          <el-form-item v-if="activeData_.range !== undefined" label="范围选择">
            <el-switch v-model="activeData_.range" @change="rangeChange" />
          </el-form-item>
          <el-form-item
            v-if="
              activeData_.border !== undefined &&
              activeData_.optionType === 'default'
            "
            label="是否带边框"
          >
            <el-switch v-model="activeData_.border" />
          </el-form-item>
          <el-form-item
            v-if="activeData_.tag === 'el-color-picker'"
            label="颜色格式"
          >
            <el-select
              v-model="activeData_['color-format']"
              placeholder="请选择颜色格式"
              :style="{ width: '100%' }"
              @change="colorFormatChange"
            >
              <el-option
                v-for="(item, index) in colorFormatOptions"
                :key="index"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
          </el-form-item>
          <el-form-item
            v-if="
              activeData_.size !== undefined &&
              (activeData_.optionType === 'button' ||
                activeData_.border ||
                activeData_.tag === 'el-color-picker')
            "
            label="选项尺寸"
          >
            <el-radio-group v-model="activeData_.size">
              <el-radio-button label="medium"> 中等 </el-radio-button>
              <el-radio-button label="small"> 较小 </el-radio-button>
              <el-radio-button label="mini"> 迷你 </el-radio-button>
            </el-radio-group>
          </el-form-item>
          <el-form-item
            v-if="activeData_['show-word-limit'] !== undefined"
            label="输入统计"
          >
            <el-switch v-model="activeData_['show-word-limit']" />
          </el-form-item>
          <el-form-item
            v-if="activeData_.tag === 'el-input-number'"
            label="严格步数"
          >
            <el-switch v-model="activeData_['step-strictly']" />
          </el-form-item>
          <el-form-item
            v-if="activeData_.tag === 'el-cascader'"
            label="是否多选"
          >
            <el-switch v-model="activeData_.props.props.multiple" />
          </el-form-item>
          <el-form-item
            v-if="activeData_.tag === 'el-cascader'"
            label="展示全路径"
          >
            <el-switch v-model="activeData_['show-all-levels']" />
          </el-form-item>
          <el-form-item
            v-if="activeData_.tag === 'el-cascader'"
            label="可否筛选"
          >
            <el-switch v-model="activeData_.filterable" />
          </el-form-item>
          <el-form-item
            v-if="activeData_.clearable !== undefined"
            label="能否清空"
          >
            <el-switch v-model="activeData_.clearable" />
          </el-form-item>
          <el-form-item
            v-if="activeData_.showTip !== undefined"
            label="显示提示"
          >
            <el-switch v-model="activeData_.showTip" />
          </el-form-item>
          <el-form-item
            v-if="activeData_.multiple !== undefined"
            label="多选文件"
          >
            <el-switch v-model="activeData_.multiple" />
          </el-form-item>
          <el-form-item
            v-if="activeData_['auto-upload'] !== undefined"
            label="自动上传"
          >
            <el-switch v-model="activeData_['auto-upload']" />
          </el-form-item>
          <el-form-item
            v-if="activeData_.readonly !== undefined"
            label="是否只读"
          >
            <el-switch v-model="activeData_.readonly" />
          </el-form-item>
          <el-form-item
            v-if="activeData_.disabled !== undefined"
            label="是否禁用"
          >
            <el-switch v-model="activeData_.disabled" />
          </el-form-item>
          <el-form-item
            v-if="activeData_.tag === 'el-select'"
            label="是否可搜索"
          >
            <el-switch v-model="activeData_.filterable" />
          </el-form-item>
          <el-form-item v-if="activeData_.tag === 'el-select'" label="是否多选">
            <el-switch
              v-model="activeData_.multiple"
              @change="multipleChange"
            />
          </el-form-item>
          <el-form-item
            v-if="activeData_.required !== undefined"
            label="是否必填"
          >
            <el-switch v-model="activeData_.required" />
          </el-form-item>

          <template v-if="activeData_.layoutTree">
            <el-divider>布局结构树</el-divider>
            <el-tree
              :data="[activeData_]"
              :props="layoutTreeProps"
              node-key="renderKey"
              default-expand-all
              draggable
            >
              <span slot-scope="{ node, data }">
                <span class="node-label">
                  <svg-icon class="node-icon" :icon-class="data.tagIcon" />
                  {{ node.label }}
                </span>
              </span>
            </el-tree>
          </template>

          <template
            v-if="
              activeData_.layout === 'colFormItem' &&
              activeData_.tag !== 'el-button'
            "
          >
            <el-divider>正则校验</el-divider>
            <div
              v-for="(item, index) in activeData_.regList"
              :key="index"
              class="reg-item"
            >
              <span
                class="close-btn"
                @click="activeData_.regList.splice(index, 1)"
              >
                <i class="el-icon-close" />
              </span>
              <el-form-item label="表达式">
                <el-input v-model="item.pattern" placeholder="请输入正则" />
              </el-form-item>
              <el-form-item label="错误提示" style="margin-bottom: 0">
                <el-input v-model="item.message" placeholder="请输入错误提示" />
              </el-form-item>
            </div>
            <div style="margin-left: 20px">
              <el-button
                icon="el-icon-circle-plus-outline"
                type="text"
                @click="addReg"
              >
                添加规则
              </el-button>
            </div>
          </template>
        </el-form>
        <!-- 表单属性 -->
        <el-form v-show="currentTab === 'form'" size="small" label-width="90px">
          <el-form-item label="表单名">
            <el-input
              v-model="formConf_.formRef"
              placeholder="请输入表单名（ref）"
            />
          </el-form-item>
          <el-form-item label="表单模型">
            <el-input
              v-model="formConf_.formModel"
              placeholder="请输入数据模型"
            />
          </el-form-item>
          <el-form-item label="校验模型">
            <el-input
              v-model="formConf_.formRules"
              placeholder="请输入校验模型"
            />
          </el-form-item>
          <el-form-item label="表单尺寸">
            <el-radio-group v-model="formConf_.size">
              <el-radio-button label="medium"> 中等 </el-radio-button>
              <el-radio-button label="small"> 较小 </el-radio-button>
              <el-radio-button label="mini"> 迷你 </el-radio-button>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="标签对齐">
            <el-radio-group v-model="formConf_.labelPosition">
              <el-radio-button label="left"> 左对齐 </el-radio-button>
              <el-radio-button label="right"> 右对齐 </el-radio-button>
              <el-radio-button label="top"> 顶部对齐 </el-radio-button>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="标签宽度">
            <el-input-number
              v-model="formConf_.labelWidth"
              placeholder="标签宽度"
            />
          </el-form-item>
          <el-form-item label="栅格间隔">
            <el-input-number
              v-model="formConf_.gutter"
              :min="0"
              placeholder="栅格间隔"
            />
          </el-form-item>
          <el-form-item label="禁用表单">
            <el-switch v-model="formConf_.disabled" />
          </el-form-item>
          <el-form-item label="表单按钮">
            <el-switch v-model="formConf_.formBtns" />
          </el-form-item>
          <el-form-item label="显示未选中组件边框">
            <el-switch v-model="formConf_.unFocusedComponentBorder" />
          </el-form-item>
        </el-form>
      </el-scrollbar>
    </div>

    <treeNode-dialog
      :visible.sync="dialogVisible"
      title="添加选项"
      @commit="addNode"
    />
    <icons-dialog
      :visible.sync="iconsVisible"
      :current="activeData_[currentIconModel]"
      @select="setIcon"
    />
  </div>
</template>

<script>
// import { isArray } from "util";
import draggable from "vuedraggable";
import TreeNodeDialog from "./TreeNodeDialog";
import { isNumberStr } from "@/utils/index";
import IconsDialog from "./IconsDialog";
import {
  inputComponents,
  selectComponents,
  // layoutComponents,
} from "@/utils/generator/config";

const isArray = Array.isArray;

const dateTimeFormat = {
  date: "yyyy-MM-dd",
  week: "yyyy 第 WW 周",
  month: "yyyy-MM",
  year: "yyyy",
  datetime: "yyyy-MM-dd HH:mm:ss",
  daterange: "yyyy-MM-dd",
  monthrange: "yyyy-MM",
  datetimerange: "yyyy-MM-dd HH:mm:ss",
};

export default {
  components: {
    draggable,
    TreeNodeDialog,
    IconsDialog,
  },
  props: ["showField", "activeData", "formConf"],
  data() {
    return {
      activeData_: this.activeData,
      formConf_: this.formConf,

      currentTab: "field",
      currentNode: null,
      dialogVisible: false,
      iconsVisible: false,
      currentIconModel: null,
      dateTypeOptions: [
        {
          label: "日(date)",
          value: "date",
        },
        {
          label: "周(week)",
          value: "week",
        },
        {
          label: "月(month)",
          value: "month",
        },
        {
          label: "年(year)",
          value: "year",
        },
        {
          label: "日期时间(datetime)",
          value: "datetime",
        },
      ],
      dateRangeTypeOptions: [
        {
          label: "日期范围(daterange)",
          value: "daterange",
        },
        {
          label: "月范围(monthrange)",
          value: "monthrange",
        },
        {
          label: "日期时间范围(datetimerange)",
          value: "datetimerange",
        },
      ],
      colorFormatOptions: [
        {
          label: "hex",
          value: "hex",
        },
        {
          label: "rgb",
          value: "rgb",
        },
        {
          label: "rgba",
          value: "rgba",
        },
        {
          label: "hsv",
          value: "hsv",
        },
        {
          label: "hsl",
          value: "hsl",
        },
      ],
      justifyOptions: [
        {
          label: "start",
          value: "start",
        },
        {
          label: "end",
          value: "end",
        },
        {
          label: "center",
          value: "center",
        },
        {
          label: "space-around",
          value: "space-around",
        },
        {
          label: "space-between",
          value: "space-between",
        },
      ],
      layoutTreeProps: {
        label(data) {
          return data.componentName || `${data.label}: ${data.vModel}`;
        },
      },
    };
  },
  watch: {
    activeData(newVal) {
      this.activeData_ = newVal;
    },
    // eslint-disable-next-line no-unused-vars
    activeData_(newVal) {
      // this.$emit("change-activeData", newVal);
    },
  },
  computed: {
    documentLink() {
      return (
        this.activeData_.document ||
        "https://element.eleme.cn/#/zh-CN/component/installation"
      );
    },
    dateOptions() {
      if (
        this.activeData_.type !== undefined &&
        this.activeData_.tag === "el-date-picker"
      ) {
        if (this.activeData_["start-placeholder"] === undefined) {
          return this.dateTypeOptions;
        }
        return this.dateRangeTypeOptions;
      }
      return [];
    },
    tagList() {
      return [
        {
          label: "输入型组件",
          options: inputComponents,
        },
        {
          label: "选择型组件",
          options: selectComponents,
        },
      ];
    },
  },
  created() {
    console.log(this.activeData);
    console.log(this.activeData_);
  },
  methods: {
    addReg() {
      this.activeData_.regList.push({
        pattern: "",
        message: "",
      });
    },
    addSelectItem() {
      this.activeData_.options.push({
        label: "",
        value: "",
      });
    },
    addTreeItem() {
      ++this.idGlobal;
      this.dialogVisible = true;
      this.currentNode = this.activeData_.options;
    },
    renderContent(h, { node, data }) {
      return (
        <div class="custom-tree-node">
          <span>{node.label}</span>
          <span class="node-operation">
            <i
              on-click={() => this.append(data)}
              class="el-icon-plus"
              title="添加"
            ></i>
            <i
              on-click={() => this.remove(node, data)}
              class="el-icon-delete"
              title="删除"
            ></i>
          </span>
        </div>
      );
    },
    append(data) {
      if (!data.children) {
        this.$set(data, "children", []);
      }
      this.dialogVisible = true;
      this.currentNode = data.children;
    },
    remove(node, data) {
      const { parent } = node;
      const children = parent.data.children || parent.data;
      const index = children.findIndex((d) => d.id === data.id);
      children.splice(index, 1);
    },
    addNode(data) {
      this.currentNode.push(data);
    },
    setOptionValue(item, val) {
      item.value = isNumberStr(val) ? +val : val;
    },
    setDefaultValue(val) {
      if (Array.isArray(val)) {
        return val.join(",");
      }
      if (["string", "number"].indexOf(val) > -1) {
        return val;
      }
      if (typeof val === "boolean") {
        return `${val}`;
      }
      return val;
    },
    onDefaultValueInput(str) {
      if (isArray(this.activeData_.defaultValue)) {
        // 数组
        this.$set(
          this.activeData_,
          "defaultValue",
          str.split(",").map((val) => (isNumberStr(val) ? +val : val))
        );
      } else if (["true", "false"].indexOf(str) > -1) {
        // 布尔
        this.$set(this.activeData_, "defaultValue", JSON.parse(str));
      } else {
        // 字符串和数字
        this.$set(
          this.activeData_,
          "defaultValue",
          isNumberStr(str) ? +str : str
        );
      }
    },
    onSwitchValueInput(val, name) {
      if (["true", "false"].indexOf(val) > -1) {
        this.$set(this.activeData_, name, JSON.parse(val));
      } else {
        this.$set(this.activeData_, name, isNumberStr(val) ? +val : val);
      }
    },
    setTimeValue(val, type) {
      const valueFormat = type === "week" ? dateTimeFormat.date : val;
      this.$set(this.activeData_, "defaultValue", null);
      this.$set(this.activeData_, "value-format", valueFormat);
      this.$set(this.activeData_, "format", val);
    },
    spanChange(val) {
      this.formConf_.span = val;
    },
    multipleChange(val) {
      this.$set(this.activeData_, "defaultValue", val ? [] : "");
    },
    dateTypeChange(val) {
      this.setTimeValue(dateTimeFormat[val], val);
    },
    rangeChange(val) {
      this.$set(
        this.activeData_,
        "defaultValue",
        val
          ? [this.activeData_.min, this.activeData_.max]
          : this.activeData_.min
      );
    },
    rateTextChange(val) {
      if (val) this.activeData_["show-score"] = false;
    },
    rateScoreChange(val) {
      if (val) this.activeData_["show-text"] = false;
    },
    colorFormatChange(val) {
      this.activeData_.defaultValue = null;
      this.activeData_["show-alpha"] = val.indexOf("a") > -1;
      this.activeData_.renderKey = +new Date(); // 更新renderKey,重新渲染该组件
    },
    openIconsDialog(model) {
      this.iconsVisible = true;
      this.currentIconModel = model;
    },
    setIcon(val) {
      this.activeData_[this.currentIconModel] = val;
    },
    tagChange(tagIcon) {
      let target = inputComponents.find((item) => item.tagIcon === tagIcon);
      if (!target)
        target = selectComponents.find((item) => item.tagIcon === tagIcon);
      this.$emit("tag-change", target);
    },
  },
};
</script>

<style lang="scss" scoped>
.right-board {
  width: 350px;
  position: absolute;
  right: 0;
  top: 0;
  padding-top: 3px;
  .field-box {
    position: relative;
    height: calc(100vh - 42px);
    box-sizing: border-box;
    overflow: hidden;
  }
  .el-scrollbar {
    height: 100%;
  }
}
.select-item {
  display: flex;
  border: 1px dashed #fff;
  box-sizing: border-box;
  & .close-btn {
    cursor: pointer;
    color: #f56c6c;
  }
  & .el-input + .el-input {
    margin-left: 4px;
  }
}
.select-item + .select-item {
  margin-top: 4px;
}
.select-item.sortable-chosen {
  border: 1px dashed #409eff;
}
.select-line-icon {
  line-height: 32px;
  font-size: 22px;
  padding: 0 4px;
  color: #777;
}
.option-drag {
  cursor: move;
}
.time-range {
  .el-date-editor {
    width: 227px;
  }
  ::v-deep .el-icon-time {
    display: none;
  }
}
.document-link {
  position: absolute;
  display: block;
  width: 26px;
  height: 26px;
  top: 0;
  left: 0;
  cursor: pointer;
  background: #409eff;
  z-index: 1;
  border-radius: 0 0 6px 0;
  text-align: center;
  line-height: 26px;
  color: #fff;
  font-size: 18px;
}
.node-label {
  font-size: 14px;
}
.node-icon {
  color: #bebfc3;
}
</style>
