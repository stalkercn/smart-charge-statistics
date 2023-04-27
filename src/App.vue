<template>
  <img alt="Vue logo" src="./assets/logo.png" />
  {{ JSON.stringify(data) }}
</template>

<script setup>
import data from "./data";
import Decimal from "decimal.js";
import dayjs from "dayjs";

const parsedData = data.map((line) => {
  const lineArr = line.split(",");
  return { a: lineArr[0], b: lineArr[1], c: lineArr[2] };
});
const totalBalance = parsedData.reduce(
  (acc, line) => acc.plus(new Decimal(line.a)),
  new Decimal(0)
);
const totalEnergy = parsedData.reduce(
  (acc, line) => acc.plus(new Decimal(line.b)),
  new Decimal(0)
);
const [dates, months] = [];
const [baseDate, baseMonth] = [dayjs("2021-12-01"), dayjs("2021-12-01")];
const daysPassed = dayjs().diff("2021-12-01", "day");
const monthsPassed = Math.ceil(dayjs().diff("2021-12-01", "month"));
for (let i = 0; i <= daysPassed; i++) {
  const day = baseDate.add(i, "days");
  const filteredDataDay = parsedData.filter((line) =>
    dayjs(line.c).isSame(day, "day")
  );
  const [baseBalance, baseEngery] = [0, 0];
  filteredDataDay.forEach((line) => {
    if (dayjs(line.c).isSame(day, "day")) {
      dates.push(day.format("YYYY-M-D"), Number(line.a), Number(line.b));
    }
  });
}
const [dates, months, dateBalance, dateEnergy, monthBalance, monthEnergy] = [
  [],
  [],
  [],
  [],
  [],
  [],
];
parsedData.forEach((line) => {
  const date = dayjs(line.c).format("YYYY-M-D");
  const month = dayjs(line.c).format("YYYY-MM");
  const indexDate = dates.indexOf(date);
  const indexMonth = months.indexOf(month);

  if (indexDate >= 0) {
    dateBalance[indexDate] += dateBalance[indexDate].plus(Decimal(line.a));
    dateEnergy[indexDate] = dateEnergy[indexDate].plus(Decimal(line.b));
  } else {
    dates.push(line.c);
    dateBalance.push(Decimal(line.a));
    dateEnergy.push(Decimal(line.b));
  }
  if (indexMonth >= 0) {
    monthBalance[indexMonth] = monthBalance[indexMonth].plus(Decimal(line.a));
    monthEnergy[indexMonth] = monthEnergy[indexMonth].plus(Decimal(line.b));
  } else {
    months.push(month);
    monthBalance.push(Decimal(line.a));
    monthEnergy.push(Decimal(line.b));
  }
});
const dateBalanceDouble = dateBalance.map((bal) => Number(bal.valueOf()));
const monthBalanceDouble = monthBalance.map((bal) => Number(bal.valueOf()));
console.log(dates, months, dateBalanceDouble, monthBalanceDouble);
// const dateSummary = dates.reduce((acc, date, index) => {
//   const date = dayjs(line.c).format("YYYY-M-D");
//   const obj = acc.find((o) => o.c);
//   return acc;
// }, []);
console.log(parsedData);
console.log(totalBalance.toString());
console.log(totalEnergy.toString());
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
