<!--
/**
 * @fileoverview Task component
 * @license MIT
 * @author Rafal Pospiech <neuronet.io@gmail.com>
 * @package GanttElastic
 */
-->
<template>
  <g
    class="gantt-elastic__chart-row-bar-wrapper gantt-elastic__chart-row-task-wrapper"
    :style="{
      ...root.style['chart-row-bar-wrapper'],
      ...root.style['chart-row-task-wrapper'],
      ...task.style['chart-row-bar-wrapper']
    }"
  >
    <svg
      class="gantt-elastic__chart-row-bar gantt-elastic__chart-row-task"
      :style="{ ...root.style['chart-row-bar'], ...root.style['chart-row-task'], ...task.style['chart-row-bar'] }"
      :x="task.xP"
      :y="task.yP"
      :width="task.widthE"
      :height="task.height"
      :viewBox="`0 0 ${task.widthE} ${task.height}`"
      @click="emitEvent('click', $event)"
      @mouseenter="emitEvent('mouseenter', $event)"
      @mouseover="emitEvent('mouseover', $event)"
      @mouseout="emitEvent('mouseout', $event)"
      @mousemove="emitEvent('mousemove', $event)"
      @mousedown="emitEvent('mousedown', $event)"
      @mouseup="emitEvent('mouseup', $event)"
      @mousewheel="emitEvent('mousewheel', $event)"
      @touchstart="emitEvent('touchstart', $event)"
      @touchmove="emitEvent('touchmove', $event)"
      @touchend="emitEvent('touchend', $event)"
      xmlns="http://www.w3.org/2000/svg"
    >
      <defs>
        <clipPath :id="clipPathId">
          <polygon :points="getPoints"></polygon>
        </clipPath>
      </defs>
      <polygon
        class="gantt-elastic__chart-row-bar-polygon gantt-elastic__chart-row-bar-polygon-estimated"
        :style="{
          ...root.style['chart-row-bar-polygon'],
          ...root.style['chart-row-task-polygon'],
          ...task.style['base'],
          ...task.style['chart-row-bar-polygon-estimated']
        }"
        :points="getPoints"
      ></polygon>
    </svg>
  </g>
</template>

<script>
import taskMixin from './Task.mixin.js';
export default {
  name: 'Task',
  inject: ['root'],
  props: ['task'],
  mixins: [taskMixin],
  data() {
    return {};
  },
  computed: {
    /**
     * Get clip path id
     *
     * @returns {string}
     */
    clipPathId() {
      return 'gantt-elastic__task-clip-path-' + this.task.id;
    },

    /**
     * Get points
     *
     * @returns {string}
     */
    getPoints() {
      const task = this.task;
      return `0,0 ${task.widthE},0 ${task.widthE},${task.height} 0,${task.height}`;
    }
  }
};
</script>
