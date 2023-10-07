<script setup>
import { ref, nextTick } from 'vue';
import { Select } from '@/components';
const props = defineProps(['onSubmit', 'active', 'closeModal']);

const renderComponent = ref(true);

const inpFullName = ref('');
const inpCity = ref('');
const inpBirthday = ref('');
const inpInn = ref('');
const inpGender = ref('male');
const inpType = ref('ТД');
const inpCountry = ref('ru');
const inpPosition = ref('mountaineer');
const inpStatus = ref('remark');


const setSelectStatus = (e) => inpStatus.value = e.value || 'remark';
const setSelectGender = (e) => inpGender.value = e.value || 'male';
const setSelectType = (e) => inpType.value = e.value || 'ТД';
const setSelectCountry = (e) => inpCountry.value = e.value || 'ru';
const setSelectPosition = (e) => inpPosition.value = e.value || 'mountaineer';

const selectContactData = [
	{ preview: "ТД", value: 'ТД' },
	{ preview: "ГПХ", value: 'ГПХ' },
	{ preview: "СМЗ", value: 'СМЗ' },
	{ preview: "Кандидат", value: 'Кандидат' }
];
const selectStatusData = [
	{ preview: "Пропустил медосмотр", value: 'remark' },
	{ preview: "Истекает патент", value: 'problem' },
	{ preview: "Истекают все документы", value: 'critical' },
	{ preview: "Прошел все процедуры", value: 'done' },
];
const selectGenderData = [
	{ preview: "Мужской", value: 'male' },
	{ preview: "Женский", value: 'female' }
];
const selectCountryData = [
	{ preview: "Россия", value: 'ru' },
	{ preview: "Узбекистан", value: 'uz' },
	{ preview: "Таджикистан", value: 'tj' }
];
const selectPositionData = [
	{ preview: "Промышленный альпинист", value: 'mountaineer' },
	{ preview: "Токарь", value: 'Turner' },
	{ preview: "Пекарь", value: 'Baker' }
];


async function closeForm() {
	if (typeof props.closeModal === 'function') props.closeModal();
	renderComponent.value = false;
	await nextTick();
	renderComponent.value = true;
}

function formSubmit(e) {
	e.preventDefault();

	let data = {
		"country": inpCountry.value,
		"position": inpPosition.value,
		"gender": inpGender.value,
		"type": inpType.value,
		"fullname": inpFullName.value,
		"inn": inpInn.value,
		"address": inpCity.value,
		"birthday": new Date(inpBirthday.value).getTime(),
		"status": inpStatus.value
	}

	if (typeof props.onSubmit === 'function') props.onSubmit(data);

	closeForm();
}

</script>

<template>
	<div class="modal" :data-active="props.active">
		<form autocomplete="off" class="modal__form" @reset="closeForm" @submit="formSubmit">
			<input autocomplete="off" placeholder="ФИО" required type="text" v-model="inpFullName">
			<input autocomplete="off" placeholder="Город" required type="text" v-model="inpCity">
			<input autocomplete="off" placeholder="ИНН" required type="text" v-model="inpInn">
			<input autocomplete="off" required type="date" v-model="inpBirthday">
			<Select :onChange="setSelectStatus" label="preview" :options="selectStatusData" />
			<Select :onChange="setSelectGender" label="preview" :options="selectGenderData" />
			<Select :onChange="setSelectCountry" label="preview" :options="selectCountryData" />
			<Select :onChange="setSelectPosition" label="preview" :options="selectPositionData" />
			<Select :onChange="setSelectType" label="preview" :options="selectContactData" />
			<button type="reset">Закрыть</button>
			<button type="submit">Добавить</button>
		</form>
	</div>
</template>


<style lang="scss" scoped>
@import "./style.scss";
</style>
