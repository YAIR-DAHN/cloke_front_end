<template>
    <div id="Container">
        <div id="listContainer">
            <h1>רשימת אברכים</h1>
            <ul>
                <li v-for="(item, index) in listNames" :key="item">
                    <button class="button" @click="getUserData(index)">{{ item }}</button>
                </li>
            </ul>
        </div>

        <div v-show="!showTable" id="noSelct">
            <h1>בכדי להציג את הנתונים <br> יש לבחור אברך להצגה <br> מתוך הרשימה מצד ימין</h1>
        </div>

        <div id="tebleContainer" v-show="showTable">
            <h1>{{ name }}</h1>
            <active-table id="active-table" :isCellTextEditable="false" :displayAddNewColumn="false"
                :displayAddNewRow="false" :data="data" :headerStyles="{ default: { backgroundColor: '#d6d6d630' } }" :files='{
                    "buttons": [
                        { "export": { "fileName": "example_xlsx", "formats": ["xlsx"] } },
                        { "import": { "fileName": "example_xlsx", "formats": ["xlsx"] } }
                    ]
                }' :customColumnsSettings='[
    { "headerName": "תאריך", "defaultText": "תאריך", "cellStyle": { "width": "100px", "text-align": "center" }, "defaultColumnTypeName": "Date y-m-d" },
    { "headerName": "ת. עברי", "defaultText": "ת. עברי", "cellStyle": { "backgroundColor": "#f1f1f1", "width": "120px", "text-align": "center", "fontWeight": "bold" } },
    { "headerName": "יום", "cellStyle": { "backgroundColor": "#fdfdfd", "width": "60px", "text-align": "center" }, "defaultColumnTypeName": "Text" },
    { "headerName": "כניסה 1", "cellStyle": { "backgroundColor": "#f8f8f8", "width": "100px", "text-align": "center" }, "defaultColumnTypeName": "Custom type" },
    { "headerName": "יציאה 1", "cellStyle": { "backgroundColor": "#fdfdfd", "width": "100px", "text-align": "center" }, "defaultColumnTypeName": "Text" },
    { "headerName": "כניסה 2", "cellStyle": { "backgroundColor": "#f8f8f8", "width": "100px", "text-align": "center" }, "defaultColumnTypeName": "Text" },
    { "headerName": "יציאה 2", "cellStyle": { "backgroundColor": "#fdfdfd", "width": "100px", "text-align": "center" }, "defaultColumnTypeName": "Text" },
    { "headerName": "כניסה 3", "cellStyle": { "backgroundColor": "#f8f8f8", "width": "100px", "text-align": "center" }, "defaultColumnTypeName": "Text" },
    { "headerName": "יציאה 3", "cellStyle": { "backgroundColor": "#fdfdfd", "width": "100px", "text-align": "center" }, "defaultColumnTypeName": "Text" },

]' :customColumnTypes='[
    {
        "name": "Custom type",
        "textValidation": {
            "func": (cellText) => this.isTimeGreaterThan(cellText, this.inBonusTime),
            "setTextToDefaultOnFail": false,
            "failedStyle": { "color": "green", "fontWeight": "bold" }
        }
    }
]'
                :tableStyle='{ "borderRadius": "10px", "border": "1px solid black", "width": "95%", "height": "100%", "margin": "0 auto", "boxSizing": "border-box", "padding": "0", "fontFamily": "sans-serif", "textAlign": "center" }' />

        </div>
    </div>
</template>
  
<script>
import "active-table";
import axios from 'axios';
import * as xlsx from "xlsx";

window.XLSX = xlsx;

export default {
    name: "App",
    data() {
        return {
            // data: [
            //     ["תאריך", "ת. עברי", "יום", "כניסה 1", "יציאה 1", "כניסה 2", "יציאה 2", "כניסה 3", "יציאה 3"],
            //     ["2019-12-26", "כ״ח כסלו תש״פ", "חמישי", "09:23:44", "13:05:19", "14:06:31", "16:01:13"],
            //     ["2019-12-29", "א׳ טבת תש״פ", "ראשון", "09:39:55", "13:00:33", "13:42:28"],
            //     ["2019-12-30", "ב׳ טבת תש״פ", "שני", "09:08:43", "13:00:43", "14:22:08", "18:02:53", "19:55:30", "21:50:24"],
            //     ["2019-12-31", "ג׳ טבת תש״פ", "שלישי", "09:08:43", "13:00:43", "14:22:08", "18:02:53", "19:55:30", "21:50:24"],
            //     ["2020-01-01", "ד׳ טבת תש״פ", "רביעי", "09:25:28", "13:04:58", "14:33:06", "18:00:15", "19:47:35", "21:22:15"],
            //     ["2020-01-02", "ה׳ טבת תש״פ", "חמישי", "09:52:03", "13:00:45", "14:31:07", "18:01:22", "20:02:47", "21:20:55"],
            //     ["2020-01-07", "י׳ טבת תש״פ", "שלישי", "09:55:35", "13:12:27"],
            //     ["2020-01-09", "י״ב טבת תש״פ", "חמישי", "09:48:37", "13:00:58", "14:27:06", "18:17:47"],
            //     ["2020-01-12", "ט״ו טבת תש״פ", "ראשון", "09:42:57", "13:02:10", "14:33:40", "18:16:07"],
            //     ["2020-01-13", "ט״ז טבת תש״פ", "שני", "09:42:57", "13:02:10", "14:33:40", "18:16:07"],
            //     ["2020-01-14", "י״ז טבת תש״פ", "שלישי", "09:42:57", "13:02:10", "14:33:40", "18:16:07"],
            //     ["2020-01-15", "י״ח טבת תש״פ", "רביעי", "09:04:48", "13:00:37", "14:29:25", "18:19:03"],
            //     ["2020-01-22", "כ״ה טבת תש״פ", "רביעי", "09:20:49", "13:40:34", "14:32:49", "17:59:30"],

            // ],

            data: [], //הדאטה שמוצגת בטבלה
            resData: [], //הדאטה שמקבלים מהשרת
            listNames: {}, //רשימת האברכים
            showTable: false, //האם להציג את הטבלה
            name: "", //שם האברך שנבחר
            receivedData: [], //הדאטה שמקבלים מהשרת
            inBonusTime: "09:14:00", //שעת כניסה לבונוס
        };

    },

    methods: {
        getData() {
            this.resData = localStorage.getItem('data');
            this.resData = JSON.parse(this.resData);

            this.getList();

        },

        //חילוץ רשימת האברכים מתוך הדאטה
        getList() {
            let list = {};
            for (let i = 0; i < this.resData.length; i++) {
                if (this.resData[i][this.resData[i].length - 1] == "לא מוגדר") {
                    continue;
                }
                // list.push(this.resData[i][this.resData[i].length-1]);
                // console.log(list);
                list[i] = this.resData[i][this.resData[i].length - 1];

            }
            this.listNames = list;
            console.log("list", list);

        },

        //שליפת ויצירת מערך נתונים לאברך הנבחר
        getUserData(id) {
            let arryData = [this.resData[id]]; //הגדרת מערך לאברך שנבחר
            let firstItem = arryData[0][0]; //שליפת הכותרת מהשורה הראשונה במערך
            let last50Items = arryData[0].slice(-50); //חיתוך המערך ל50 שורות בלבד

            let newArray = [firstItem];//מיזוג

            // last50Items.map((item) => {
            //     newArray.push(item);
            // })

            //הכנסת השורות בהיפוך, תוך התעלמות מהשורה האחרונה
            let index = last50Items.length-2;
            for (let i = index ; i > 0; i--) {
                newArray.push(last50Items[i]);
            }


            newArray.pop() //הסרת שם האברך מהשורה האחרונה
            let arryFormat = []
            arryFormat.push(newArray);

            this.updateData(newArray);
            this.name = this.listNames[id];
        },

        //עדכון הדאטה בטבלה
        updateData(data) {
            const tableElement = document.getElementById('active-table');
            tableElement.updateData(data);
            this.showTable = true;

        },

        //השוואה של שעות
        isTimeGreaterThan(time1, time2) {
            // המרת המחרוזות לאובייקטים Date
            const date1 = new Date(`1970-01-01T${time1}Z`);
            const date2 = new Date(`1970-01-01T${time2}Z`);

            console.log(date1);
            // השוואת האובייקטים Date
            if (date1 != "Invalid Date" && date2 != "Invalid Date") {
                return date1 > date2;
            }

        },

    },
    mounted() {
        this.getData();
    },

};
</script>
  
<style>
#Container {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr;
    grid-template-areas: "listContainer tebleContainer tebleContainer";
    width: 100%;
    height: 100%;
    margin: 0 auto;
    box-sizing: border-box;
    padding: 0;
    font-family: sans-serif;
    text-align: center;

}

#listContainer {
    height: 100%;
    padding: 15px;
    font-family: sans-serif;
    text-align: center;
    /* border: 1px solid black;
    border-radius: 20px; */
    /* margin-right: 50px; */
}

#noSelct {
    grid-column: 2/7;
    height: 100%;
    box-sizing: border-box;
    padding: 5px;
    font-family: sans-serif;
    text-align: center;
}

#tebleContainer {
    /* grid-area: tebleContainer; */
    grid-column: 2/7;
    height: 100%;
    box-sizing: border-box;
    padding: 5px;
    font-family: sans-serif;
    text-align: center;
    /* border: 1px solid black;
    border-radius: 20px; */
    /* margin-left: 50px; */
}

ul {
    list-style-type: none;
    padding: 0;
    margin: 0;
    display: flex;
    flex-direction: column;
    justify-content: normal;
    align-items: center;
    width: 100%;
    height: 100%;
    box-sizing: border-box;
    padding: 0;
    font-family: sans-serif;
    text-align: center;
}

div {
    font-family: sans-serif;
    text-align: center;
}

active-table {
    margin: 0 auto;
    box-sizing: none;
}

.button {
    background-color: #1976d2;
    /* Blue */
    border: none;
    color: white;
    padding: 10px 20px;
    margin: 5px 0;
    text-align: center;
    text-decoration: none;
    font-size: 14px;
    cursor: pointer;
    width: 120px;
    display: inline-block;
    border-radius: 4px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
    transition: background-color 0.3s ease;
}

.button:hover {
    background-color: #1565c0;
    /* Darker blue */
}

.button:not(:last-child) {
    border-bottom: none;
    /* Prevent double borders */
}


/* .button:hover {
  background-color: #3e8e41;
} */
</style>
  