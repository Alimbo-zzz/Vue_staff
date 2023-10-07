<script setup>
import { ref, nextTick } from 'vue';
import { Select } from '@/components';
const renderComponent = ref(true);

const props = defineProps(['onSubmit', 'openModal']);
var filterData = ref({
	position: null,
	gender: null,
	country: null,
	type: null
});

function submitFilter() {
	if (typeof props.onSubmit === 'function') props.onSubmit(filterData);
}
const forceRender = async () => {
	renderComponent.value = false;
	await nextTick();
	renderComponent.value = true;
};

function resetFilter() {
	let obj = {};
	for (const key in filterData.value) {
		obj[key] = null;
	}
	filterData.value = obj;
	submitFilter();
	forceRender();
}


const radioContactData = [
	{ preview: "Не имеет значения", value: null },
	{ preview: "ТД", value: 'ТД' },
	{ preview: "ГПХ", value: 'ГПХ' },
	{ preview: "СМЗ", value: 'СМЗ' },
	{ preview: "Кандидат", value: 'Кандидат' }
];
const selectGenderData = [
	{ preview: "Без разницы", value: null },
	{ preview: "Мужской", value: 'male' },
	{ preview: "Женский", value: 'female' }
];
const selectCountryData = [
	{ preview: "Все страны", value: null },
	{ preview: "Россия", value: 'ru' },
	{ preview: "Узбекистан", value: 'uz' },
	{ preview: "Таджикистан", value: 'tj' }
];
const selectPositionData = [
	{ preview: "Все должности", value: null },
	{ preview: "Промышленный альпинист", value: 'mountaineer' },
	{ preview: "Токарь", value: 'Turner' },
	{ preview: "Пекарь", value: 'Baker' }
];


const setSelectPosition = (event) => filterData.value.position = event.value;
const setSelectCountry = (event) => filterData.value.country = event.value;
const setSelectGender = (event) => filterData.value.gender = event.value;
const setContactType = (event) => filterData.value.type = event.target.value;


</script>

<template>
	<bar class="filter">
		<div class="box _head">
			<button class="add-btn" @click="props.openModal">
				<img src="@icons/add-user.svg">
				Добавить нового сотрудника
			</button>
		</div>
		<hr>
		<div class="box _content" v-if="renderComponent">
			<h2>Фильтр</h2>
			<div class="filter__info">
				<label>
					<h5>Гражданство</h5>
					<Select :onChange="setSelectCountry" label="preview" :options="selectCountryData" />
				</label>
				<label>
					<h5>Пол</h5>
					<Select :onChange="setSelectGender" label="preview" :options="selectGenderData" />
				</label>
			</div>
			<div class="filter__position">
				<h5>Должность</h5>
				<Select :onChange="setSelectPosition" label="preview" :options="selectPositionData" />
			</div>
			<div class="filter__contact">
				<h5>Тип договора</h5>
				<label v-for="(item, i) in radioContactData" :key="i"> <input :checked="i === 0" type="radio" name="contact"
						:value="item" @change="setContactType"> {{ item.preview }}</label>
			</div>
			<hr>
			<div class="filter__btns">
				<button data-name="submit" @click="submitFilter">Применить</button>
				<button data-name="reset" @click="resetFilter">Очистить</button>
			</div>
		</div>
	</bar>
</template>


<style lang="scss" scoped>
@import "./style.scss";
</style>
