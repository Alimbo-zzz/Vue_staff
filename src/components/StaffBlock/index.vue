<script setup>
// import { computed, ref, watch, onMounted } from 'vue';
const props = defineProps(['data', 'pagination']);

const genderData = [
	{ preview: "Без разницы", value: null },
	{ preview: "Мужской", value: 'male' },
	{ preview: "Женский", value: 'female' }
];
const countryData = [
	{ preview: "Все страны", value: null },
	{ preview: "Россия", value: 'ru' },
	{ preview: "Узбекистан", value: 'uz' },
	{ preview: "Таджикистан", value: 'tj' }
];
const positionData = [
	{ preview: "Все должности", value: null },
	{ preview: "Промышленный альпинист", value: 'mountaineer' },
	{ preview: "Токарь", value: 'Turner' },
	{ preview: "Пекарь", value: 'Baker' }
];


const getPagText = (status) => props.pagination.find(el => el.value === status)?.preview;
const getPositionText = (position) => positionData.find(el => el.value === position)?.preview;
const getGenderText = (gender) => genderData.find(el => el.value === gender)?.preview;
const getFlagIcon = (flag) => new URL(`../../assets/icons/flags/${flag}.svg`, import.meta.url).href;
const getDateText = (date_unix, type = 'fullDate') => {
	if (type === 'fullDate') {
		const date = new Date(date_unix);

		let day = String(date.getDate()).padStart(2, '0');
		let month = String(date.getMonth()).padStart(2, '0');
		let year = String(date.getFullYear());

		return `${day}.${month}.${year} г.`
	}

	if (type === 'age') {
		var now = new Date(); //Текущя дата
		var today = new Date(now.getFullYear(), now.getMonth(), now.getDate()); //Текущя дата без времени
		var dob = new Date(date_unix); //Дата рождения
		var dobnow = new Date(today.getFullYear(), dob.getMonth(), dob.getDate()); //ДР в текущем году
		var age; //Возраст

		//Возраст = текущий год - год рождения
		age = today.getFullYear() - dob.getFullYear();
		//Если ДР в этом году ещё предстоит, то вычитаем из age один год
		if (today < dobnow) {
			age = age - 1;
		}
		return age;
	}
}

</script>

<template>
	<ul class="staff-list">
		<li class="staff-list__item" v-for="(item, i) in props.data" :key="i">
			<div class="staff-list__box">
				<h4 data-name="fullname">{{ item.fullname }}</h4>
				<span data-name="inn">ИНН {{ item.inn }}</span>
				<span data-name="type">{{ item.type }}</span>
				<p data-name="position">{{ getPositionText(item.position) }}</p>
			</div>
			<div class="staff-list__box" data-name="middle">
				<p data-name="country"> <img :src="getFlagIcon(item.country)"> {{ item.country.toUpperCase() }} 4002571654</p>
				<p data-name="city">{{ item.address }}</p>
				<p data-name="birth">Дата рождения: {{ getDateText(item.birthday) }}</p>
				<p data-name="age">Возраст: {{ getDateText(item.birthday, 'age') }} лет</p>
				<p data-name="gender">Пол: {{ getGenderText(item.gender) }}</p>
			</div>
			<div class="staff-list__box">
				<span data-name="status" :data-status="item.status">{{ getPagText(item.status) }}</span>
			</div>
		</li>
	</ul>
</template>


<style lang="scss" scoped>
@import "./style.scss";
</style>
