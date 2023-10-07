<script setup>
import { ref, watch, onMounted } from 'vue';
const props = defineProps(['options', 'label', 'onChange', 'className']);
const activeIndex = ref(props.index || 0);
const isOpen = ref(false);
const activeValue = ref(props.value || props.options[activeIndex])


watch(activeIndex, (newValue, oldValue) => {
	if (typeof props.onChange === 'function') props.onChange(props.options[newValue], props.options[oldValue])
	activeValue.value = props.options[newValue];
})



const selectClose = () => isOpen.value = false;
const selectOpen = () => isOpen.value = true;
const selectToggle = () => isOpen.value = !isOpen.value;
const changeValue = (index) => { activeIndex.value = index; selectClose(); };


onMounted(() => {
	document.addEventListener('click', selectClose);
	props.onChange(activeValue, null);

	return () => {
		document.removeEventListener('click', selectClose)
	}
})


</script>


<template>
	<div :class="`select ${className || ''}`" @click="(e) => e.stopPropagation()">
		<div class="select__preview" :data-open="isOpen" @click="selectToggle">
			{{ props.options[activeIndex][label] || props.options[activeIndex] }}
		</div>
		<ul class="select__list" :data-open="isOpen">
			<li class="select__item" v-for="(item, i) in props.options" :key="i" :data-active="activeIndex === i"
				@click="changeValue(i)">
				{{ item[label] || item }}
			</li>
		</ul>
	</div>
</template>


<style lang="scss" scoped>
@import "./style.scss";
</style>
