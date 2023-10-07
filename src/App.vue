<script setup>
import { ref, watch } from 'vue';
import { StaffBlock, FilterBlock, ModalForm } from '@/components';
import staffJSON from '@/assets/data/staff.json';

const staffData = ref([...staffJSON.staff]);
const staffFiltered = ref([...staffData.value]);
const staffTags = ref([...staffFiltered.value]);
const staffSearch = ref([...staffTags.value]);


var modalFormIsOpen = ref(false);
const searchInpValue = ref('');


watch(staffData, newValue => staffFiltered.value = newValue);
watch(staffFiltered, newValue => staffTags.value = newValue);
watch(staffTags, newValue => staffSearch.value = newValue);


const pagination = ref([
	{ value: 'all', name: 'Весь список', preview: "", active: true, },
	{ value: 'problem', name: 'Проблемные', preview: "Истекает патент", active: false, },
	{ value: 'critical', name: 'Критические', preview: "Истекают все документы", active: false, },
	{ value: 'remark', name: 'Есть замечания', preview: "Пропустил медосмотр", active: false, },
	{ value: 'done', name: 'Выполнено', preview: "Прошел все процедуры", active: false, },
])

watch(searchInpValue, str => {
	let newArray = staffTags.value.filter(el => String(el.fullname).toLowerCase().search(str.toLowerCase()) !== -1);
	staffSearch.value = newArray;
})

watch(pagination, newValue => {
	let newArray = [];
	let isAll = newValue.find(el => el.value === 'all' && el.active === true);
	if (isAll) return staffTags.value = staffFiltered.value;

	newValue.forEach((el, i) => {
		if (el.active === false) return;
		let value = el.value;
		let filtered = staffFiltered.value.filter(el => el.status === value);
		newArray = [...newArray, ...filtered];
	})
	staffTags.value = newArray;
})

function infoFilter(data) {
	let newArray = staffData.value.filter(
		(el) => {
			const { type, gender, position, country } = el;
			let isValid = true;

			if (data.value?.type && type != data.value?.type) isValid = false;
			if (data.value?.gender && gender != data.value?.gender) isValid = false;
			if (data.value?.position && position != data.value?.position) isValid = false;
			if (data.value?.country && country != data.value?.country) isValid = false;

			return isValid;
		}
	)

	staffFiltered.value = newArray;
}


const setPagination = (pag_value) => {
	if (pag_value === 'all') {
		let newArr = pagination.value.map(el => {
			el.active = false;
			(el.value === 'all') && (el.active = true);
			return el;
		})

		pagination.value = newArr
	}
	else {
		let newArr = pagination.value.map(el => {
			(el.value === pag_value) && (el.active = !el.active);
			(el.value === 'all') && (el.active = false);
			return el;
		})

		pagination.value = newArr;
	}
}

const openModal = (e) => modalFormIsOpen.value = true;
const closeModal = (e) => modalFormIsOpen.value = false;


const addStaff = (data) => {
	console.log(data)
	let newData = [{ ...data }, ...staffData.value];
	staffData.value = newData;
}

</script>

<template>
	<ModalForm :active="modalFormIsOpen" :closeModal="closeModal" :onSubmit="addStaff" />
	<div class="wrapper">
		<div class="container">
			<div class="staff">
				<div class="box _head">
					<label>
						<input v-model="searchInpValue" type="text" placeholder="Поиск сотрудника">
						<img src="@icons/search.svg">
					</label>
					<p>Например: Ивано Иван</p>
				</div>
				<hr>
				<div class="box _content">
					<h2>Список сотрудников</h2>
					<ul class="pagination">
						<li class="pagination__item" v-for="(pag, i) in pagination" :key="i" :data-name="pag.value"
							:data-active="pag.active" @click="setPagination(pag.value)">
							<p>{{ pag.name }}</p>
						</li>
					</ul>

					<StaffBlock :data="staffSearch" :pagination="pagination" />
				</div>
			</div>

			<FilterBlock :onSubmit="infoFilter" :openModal="openModal" />
		</div>
	</div>
</template>


<style lang="scss" scoped>
@import "./main.scss";
</style>
