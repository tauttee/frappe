<template>
  <div>

    <div class="modules-container" :class="{'dragging': dragging}" ref="modules-container">
      <desk-module-box
        v-for="(module, index) in modules"
        :key="module.module_name"
        :index="index"
        v-bind="module"
		@customize="show_module_card_customize_dialog(module)"
      ></desk-module-box>
    </div>
  </div>
</template>

<script>
import DeskModuleBox from "./DeskModuleBox.vue";

export default {
	props: ['category', 'modules'],
	components: {
		DeskModuleBox
	},
	data() {
		return {
			fetched_module_links: {}
		}
	},
	mounted() {
		if (!frappe.utils.is_mobile()) {
			this.setup_sortable();
		}
	},
	methods: {
		
		update_module_order({ module_category, modules }) {
			console.log('sort')
			frappe.call('frappe.desk.moduleview.update_modules_order', { module_category, modules });
		},
		setup_sortable() {
			let modules_container =this.$refs['modules-container'];
			this.sortable = new Sortable(modules_container, {
				animation: 150,
				onStart: () => this.dragging = true,
				onEnd: () => {
					this.dragging = false;
					let modules = Array.from(modules_container.querySelectorAll('.module-box'))
						.map(node => node.dataset.moduleName);
					console.log('sorting!')
					this.update_module_order({
						module_category: this.category,
						modules
					});
				}
			})
		},
		
	}
}
</script>

<style lang="less" scoped>
.modules-container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  grid-auto-rows: minmax(62px, 1fr);
  column-gap: 8px;
  row-gap: 8px;
  align-items: center;
}
</style>
