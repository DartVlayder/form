<template>
  <div>
    <label>Город</label>
    <select v-model="selectedCity" @change="updateDependentFields">
      <option disabled value="">Выберите город...</option>
      <option v-for="city in cities" :key="city.id" :value="city.id">{{ city.name }}</option>
    </select>

    <label>Цех</label>
    <select v-model="selectedWorkshop" :disabled="!selectedCity || filteredWorkshops.length === 0">
      <option disabled value="">Выберите цех...</option>
      <option v-for="workshop in filteredWorkshops" :key="workshop.id" :value="workshop.id">{{ workshop.name }}</option>
    </select>

    <label>Сотрудник</label>
    <select v-model="selectedEmployee" :disabled="!selectedWorkshop || filteredEmployees.length === 0">
      <option disabled value="">Выберите сотрудника...</option>
      <option v-for="employee in filteredEmployees" :key="employee.id" :value="employee.id">{{ employee.name }}</option>
    </select>

    <label>Бригада</label>
    <select v-model="selectedTeam">
      <option disabled value="">Выберите бригаду...</option>
      <option v-for="team in teams" :key="team.id" :value="team.id">{{ team.name }}</option>
    </select>

    <label>Смена</label>
    <select v-model="selectedShift">
      <option disabled value="">Выберите смену...</option>
      <option v-for="shift in shifts" :key="shift.id" :value="shift.id">{{ shift.name }}</option>
    </select>
  </div>
</template>

<script>
import { ref, computed, watch } from 'vue'

export default {
  name: 'FormAs',
  setup() {
    const selectedCity = ref('');
    const selectedWorkshop = ref('');
    const selectedEmployee = ref('');
    const selectedTeam = ref('');
    const selectedShift = ref('');

    const cities = [
      { id: 1, name: 'Город 1' },
      { id: 2, name: 'Город 2' },
      { id: 3, name: 'Город 3' }
    ];

    const workshops = [
      { id: 1, name: 'Цех 1', cityId: 1 },
      { id: 2, name: 'Цех 2', cityId: 1 },
      { id: 3, name: 'Цех 1', cityId: 2 },
      { id: 4, name: 'Цех 2', cityId: 2 },
      { id: 5, name: 'Цех 1', cityId: 3 },
      { id: 6, name: 'Цех 2', cityId: 3 }
    ];

    const employees = [
      { id: 1, name: 'Сотрудник 1', workshopId: 1 },
      { id: 2, name: 'Сотрудник 2', workshopId: 1 },
      { id: 3, name: 'Сотрудник 1', workshopId: 2 },
      { id: 4, name: 'Сотрудник 2', workshopId: 2 },
      { id: 5, name: 'Сотрудник 3', workshopId: 3 },
      { id: 6, name: 'Сотрудник 4', workshopId: 3 },
      { id: 7, name: 'Сотрудник 1', workshopId: 4 },
      { id: 8, name: 'Сотрудник 2', workshopId: 4 },
      { id: 9, name: 'Сотрудник 1', workshopId: 5 },
      { id: 10, name: 'Сотрудник 2', workshopId: 5 },
      { id: 11, name: 'Сотрудник 1', workshopId: 6 },
      { id: 12, name: 'Сотрудник 2', workshopId: 6 }
    ];

    const teams = [
      { id: 1, name: 'Бригада 1' },
      { id: 2, name: 'Бригада 2' },
      { id: 3, name: 'Бригада 3' }
    ];

    const shifts = [
      { id: 1, name: 'Смена 1' },
      { id: 2, name: 'Смена 2' },
      { id: 3, name: 'Смена 3' }
    ];

    const getValueFromCookie = (cookieName) => {
      const name = `${cookieName}=`;
      const decodedCookie = decodeURIComponent(document.cookie);
      const cookieArray = decodedCookie.split(';');

      for (let i = 0; i < cookieArray.length; i++) {
        let cookie = cookieArray[i];
        while (cookie.charAt(0) === ' ') {
          cookie = cookie.substring(1);
        }
        if (cookie.indexOf(name) === 0) {
          return JSON.parse(cookie.substring(name.length, cookie.length));
        }
      }
      return '';
    };

    const saveValueToCookie = (cookieName, value) => {
      const encodedValue = encodeURIComponent(JSON.stringify(value));
      document.cookie = `${cookieName}=${encodedValue}; sameSite=Strict`;
    };

    const updateDependentFields = () => {
      selectedWorkshop.value = '';
      selectedEmployee.value = '';
      const cityId = parseInt(selectedCity.value);
      filteredWorkshops.value = workshops.filter((workshop) => workshop.cityId === cityId);
    };

    watch(selectedCity, (newValue) => { saveValueToCookie('selectedCity', newValue); });
    watch(selectedWorkshop, (newValue) => { saveValueToCookie('selectedWorkshop', newValue); });
    watch(selectedEmployee, (newValue) => { saveValueToCookie('selectedEmployee', newValue); });
    watch(selectedTeam, (newValue) => { saveValueToCookie('selectedTeam', newValue); });
    watch(selectedShift, (newValue) => { saveValueToCookie('selectedShift', newValue); });

    selectedCity.value = getValueFromCookie('selectedCity');
    selectedWorkshop.value = getValueFromCookie('selectedWorkshop');
    selectedEmployee.value = getValueFromCookie('selectedEmployee');
    selectedTeam.value = getValueFromCookie('selectedTeam');
    selectedShift.value = getValueFromCookie('selectedShift');

    const filteredWorkshops = computed(() => {
      const cityId = parseInt(selectedCity.value);
      return workshops.filter((workshop) => workshop.cityId === cityId);
    });

    const filteredEmployees = computed(() => {
      const workshopId = parseInt(selectedWorkshop.value);
      return employees.filter((employee) => employee.workshopId === workshopId);
    });

    return {
      cities,
      filteredWorkshops,
      filteredEmployees,
      teams,
      shifts,
      selectedCity,
      selectedWorkshop,
      selectedEmployee,
      selectedTeam,
      selectedShift,
      updateDependentFields
    };
  }
}
</script>



<!--
  --Создал таблицы
CREATE TABLE Контейнеры (
  ИД INT PRIMARY KEY,
  Номер INT,
  Тип NVARCHAR(255),
  Длина INT,
  Ширина INT,
  Высота INT,
  Вес INT,
  [Пустой/не пустой] BIT,
  [Дата поступления] DATETIME
);
CREATE TABLE Операции (
  ИД INT PRIMARY KEY,
  [ИД Контейнера] INT,
  [Дата начала операции] DATETIME,
  [Дата окончания операции] DATETIME,
  [Тип операции] NVARCHAR(255),
  [ФИО оператора] NVARCHAR(255),
  [Место инспекции] NVARCHAR(255)
);



-- возвращаем данные в формате JSON из первой таблицы


DECLARE @jsonData NVARCHAR(MAX);
SET @jsonData = N'[';

SELECT @jsonData = @jsonData + 
    '{' +
        '"ИД": ' + CONVERT(NVARCHAR(10), ИД) + ',' +
        '"Номер": ' + CONVERT(NVARCHAR(10), Номер) + ',' +
        '"Тип": "' + Тип + '",' +
        '"Длина": ' + CONVERT(NVARCHAR(10), Длина) + ',' +
        '"Ширина": ' + CONVERT(NVARCHAR(10), Ширина) + ',' +
        '"Высота": ' + CONVERT(NVARCHAR(10), Высота) + ',' +
        '"Вес": ' + CONVERT(NVARCHAR(10), Вес) + ',' +
        '"Пустой": ' + CASE WHEN Пустой = 1 THEN 'true' ELSE 'false' END + ',' +
        '"Дата поступления": "' + CONVERT(NVARCHAR(20), Дата_поступления, 120) + '"' +
    '},' 
FROM Контейнеры;

SET @jsonData = LEFT(@jsonData, LEN(@jsonData) - 1) + ']';

SELECT @jsonData AS JSONData;



-- возвращаем данные в формате JSON из второй таблицы

DECLARE @jsonData NVARCHAR(MAX);
SET @jsonData = N'[';

SELECT @jsonData = @jsonData + 
    '{' +
        '"ИД": ' + CONVERT(NVARCHAR(10), ИД) + ',' +
        '"ИД_Контейнера": ' + CONVERT(NVARCHAR(10), ИД_Контейнера) + ',' +
        '"Дата_начала_операции": "' + CONVERT(NVARCHAR(20), Дата_начала_операции, 120) + '",' +
        '"Дата_окончания_операции": "' + CONVERT(NVARCHAR(20), Дата_окончания_операции, 120) + '",' +
        '"Тип_операции": "' + Тип_операции + '",' +
        '"ФИО_оператора": "' + ФИО_оператора + '"' +
    '},' 
FROM Операции
WHERE ИД_Контейнера = ID; -- ID заменить на нужный нам id контейнера

SET @jsonData = LEFT(@jsonData, LEN(@jsonData) - 1) + ']';

SELECT @jsonData AS JSONData;

-->