<template>
  <div class="popover-wrapper" v-show="show">
    <slot></slot>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import { createPopper, Instance } from '@popperjs/core/lib/popper-lite';
import { Placement as PopperPlacement } from '@popperjs/core/lib/enums';

type Placement = PopperPlacement;

@Component
export default class Popover extends Vue {
  @Prop()
  readonly target!: string | HTMLElement | undefined;

  @Prop()
  readonly placement!: Placement | undefined;

  popoverInstance: Instance | null = null;

  show = false;

  get getTarget(): Element | null {
    if (this.target === undefined) {
      return null;
    }

    if (typeof this.target === 'string') {
      return document.getElementById(this.target);
    }

    return null;
  }

  mounted(): void {
    const targetEl = this.getTarget;

    if (targetEl === null) {
      console.error('vue-popover: Target is not found');

      return;
    }

    targetEl.addEventListener('click', this.clickOnTarget);

    this.popoverInstance = createPopper(targetEl, this.$el as HTMLElement, {
      placement: this.placement ?? 'top',
      modifiers: [
        {
          name: 'offset',
          options: {
            offset: [0, 8]
          }
        }
      ]
    });
  }

  clickOnTarget(): void {
    this.show = !this.show
  }

  beforeDestroy(): void {
    const targetEl = this.getTarget;

    if (targetEl !== null) {
      targetEl.removeEventListener('click', this.clickOnTarget);
    }

    if (this.popoverInstance !== null) {
      this.popoverInstance.destroy();
    }
  }
}
</script>

<style scoped lang="scss">
.popover-wrapper {
  height: 400px;
  width: 400px;
  background-color: red;
}
</style>
