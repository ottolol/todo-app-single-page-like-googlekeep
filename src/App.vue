<template>
    
    <div>
        <h1>Создать заметку</h1>

        <div>
            <div>
                <input
                    class="form-control mb-2"
                    type="text"
                    placeholder="название заметки"
                    v-model="note.name"
                />
            </div>
            <div v-if="note.type === TYPE.text">
                <textarea
                    class="form-control mb-2"
                    placeholder="описание заметки"
                    v-model="note.text"
                ></textarea>
            </div>
            <div v-if="note.type === TYPE.list">
                <div id="list">
                    <div class="d-flex align-items-center" v-for="(it, ix) in activeList" :key="ix">
                        <input
                            class="form-check-input me-2 mb-2"
                            type="checkbox"
                            v-model="it.isCompleted"
                            @change="(e) => onCheckboxChange(e, false)"
                        />
                        <input class="form-control mb-2" type="text" v-model="it.text" />
                        <button class="mb-2 border-0 bg-transparent">
                            <svg
                                xmlns="http://www.w3.org/2000/svg"
                                width="16"
                                height="16"
                                fill="currentColor"
                                class="bi bi-x"
                                viewBox="0 0 16 16"
                            >
                                <path
                                    d="M4.646 4.646a.5.5 0 0 1 .708 0L8 7.293l2.646-2.647a.5.5 0 0 1 .708.708L8.707 8l2.647 2.646a.5.5 0 0 1-.708.708L8 8.707l-2.646 2.647a.5.5 0 0 1-.708-.708L7.293 8 4.646 5.354a.5.5 0 0 1 0-.708z"
                                />
                            </svg>
                        </button>
                    </div>
                </div>
                <div class="input-group mb-2">
                    <span class="form-check-input border-0 bg-transparent">
                        <svg
                            xmlns="http://www.w3.org/2000/svg"
                            width="16"
                            height="16"
                            fill="currentColor"
                            class="bi bi-plus"
                            viewBox="0 0 16 16"
                        >
                            <path
                                d="M8 4a.5.5 0 0 1 .5.5v3h3a.5.5 0 0 1 0 1h-3v3a.5.5 0 0 1-1 0v-3h-3a.5.5 0 0 1 0-1h3v-3A.5.5 0 0 1 8 4z"
                            />
                        </svg>
                    </span>
                    <input
                        class="form-control border-0 bg-transparent"
                        type="text"
                        placeholder="новый пункт"
                        @input="add"
                    />
                </div>

                <div
                    class="mb-2"
                >{{ completedList.length }} {{ plural(completedList.length, 'отмеченных пунктов', 'отмеченный пункт', 'отмеченных пункта') }}</div>
                <div class="input-group mb-2" v-for="(it, ix) in completedList" :key="ix">
                    <div class="input-group-text bg-light">
                        <input
                            class="form-check-input mt-0"
                            type="checkbox"
                            v-model="it.isCompleted"
                            @change="(e) => onCheckboxChange(e, true)"
                        />
                    </div>
                    <input
                        class="form-control bg-light text-decoration-line-through"
                        type="text"
                        v-model="it.text"
                    />
                </div>
            </div>
            <div>
                <button type="button" class="btn btn-primary me-2" @click="save">Создать</button>
                <button class="btn btn-light border me-2" @click="changeType(TYPE.text)">
                    <svg
                        xmlns="http://www.w3.org/2000/svg"
                        width="16"
                        height="16"
                        fill="currentColor"
                        class="bi bi-card-text"
                        viewBox="0 0 16 16"
                    >
                        <path
                            d="M14.5 3a.5.5 0 0 1 .5.5v9a.5.5 0 0 1-.5.5h-13a.5.5 0 0 1-.5-.5v-9a.5.5 0 0 1 .5-.5h13zm-13-1A1.5 1.5 0 0 0 0 3.5v9A1.5 1.5 0 0 0 1.5 14h13a1.5 1.5 0 0 0 1.5-1.5v-9A1.5 1.5 0 0 0 14.5 2h-13z"
                        />
                        <path
                            d="M3 5.5a.5.5 0 0 1 .5-.5h9a.5.5 0 0 1 0 1h-9a.5.5 0 0 1-.5-.5zM3 8a.5.5 0 0 1 .5-.5h9a.5.5 0 0 1 0 1h-9A.5.5 0 0 1 3 8zm0 2.5a.5.5 0 0 1 .5-.5h6a.5.5 0 0 1 0 1h-6a.5.5 0 0 1-.5-.5z"
                        />
                    </svg> Текст
                </button>
                <button class="btn btn-light border" @click="changeType(TYPE.list)">
                    <svg
                        xmlns="http://www.w3.org/2000/svg"
                        width="16"
                        height="16"
                        fill="currentColor"
                        class="bi bi-card-checklist"
                        viewBox="0 0 16 16"
                    >
                        <path
                            d="M14.5 3a.5.5 0 0 1 .5.5v9a.5.5 0 0 1-.5.5h-13a.5.5 0 0 1-.5-.5v-9a.5.5 0 0 1 .5-.5h13zm-13-1A1.5 1.5 0 0 0 0 3.5v9A1.5 1.5 0 0 0 1.5 14h13a1.5 1.5 0 0 0 1.5-1.5v-9A1.5 1.5 0 0 0 14.5 2h-13z"
                        />
                        <path
                            d="M7 5.5a.5.5 0 0 1 .5-.5h5a.5.5 0 0 1 0 1h-5a.5.5 0 0 1-.5-.5zm-1.496-.854a.5.5 0 0 1 0 .708l-1.5 1.5a.5.5 0 0 1-.708 0l-.5-.5a.5.5 0 1 1 .708-.708l.146.147 1.146-1.147a.5.5 0 0 1 .708 0zM7 9.5a.5.5 0 0 1 .5-.5h5a.5.5 0 0 1 0 1h-5a.5.5 0 0 1-.5-.5zm-1.496-.854a.5.5 0 0 1 0 .708l-1.5 1.5a.5.5 0 0 1-.708 0l-.5-.5a.5.5 0 0 1 .708-.708l.146.147 1.146-1.147a.5.5 0 0 1 .708 0z"
                        />
                    </svg> Список
                </button>
            </div>
        </div>
    </div>

    <br />
    <h2>Мои заметки</h2>
    <div class="row row-cols-1 row-cols-sm-2 row-cols-md-4">
        <div class="col mt-2" v-for="(note, ix) in itemList" :key="ix" @click="edit(note)">
            <div class="list-group-item shadow-sm mb-2">{{ note.name }}</div>
            <div
                class="list-group-item shadow-sm text-break mb-2"
                v-if="note.type === TYPE.text"
            >{{ note.text }}</div>
            <ul class="list-group shadow-sm" v-if="note.type === TYPE.list">
                <li
                    class="lsn text-break"
                    v-for="(it, idx) in note.list.filter(i => !i.isCompleted)"
                    :key="idx"
                >
                    <span class="list-group-item" v-if="it.isCompleted === false">{{ it.text }}</span>
                    <span
                        class="list-group-item bg-light text-decoration-line-through"
                        v-if="it.isCompleted === true"
                    >{{ it.text }}</span>
                </li>
                <li
                    class="lsn text-break"
                    v-for="(it, idx) in note.list.filter(i => i.isCompleted)"
                    :key="idx"
                >
                    <span class="list-group-item" v-if="it.isCompleted === false">{{ it.text }}</span>
                    <span
                        class="list-group-item bg-light text-decoration-line-through"
                        v-if="it.isCompleted === true"
                    >{{ it.text }}</span>
                </li>
            </ul>
        </div>
    </div>

</template>

<script>

const TYPE = {
    text: 'text',
    list: 'list',
}
/*
0,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,25,26,27,28,29 - отмеченных пунктов
1,21 - отмеченный пункт
2,3,4,22,23,24 - отмеченных пункта

*/
const plural = (num, form1, form2, form3) => {
    if (num % 10 === 0 || (num % 10 >= 5 && num % 10 <= 9) || (num >= 11 && num <= 14)) return form1;
    if (num % 10 === 1) return form2;
    if (num % 10 >= 2 && num % 10 <= 4) return form3;
}

export default {
    data() {
        return {
            TYPE: TYPE,
            note: {
                name: '',
                text: '',
                list: [
                    // {
                    //     text: 'aaa',
                    //     isCompleted: false,
                    // },
                    // {
                    //     text: 'bbbb',
                    //     isCompleted: true,
                    // },
                    // {
                    //     text: 'cccc',
                    //     isCompleted: false,
                    // },
                ],
                type: TYPE.list,
            },
            itemList: [],
        }
    },
    mounted() {
        this.loadState();
    },
    watch: {
        itemList: {
            handler() {
                this.saveState();
            },
            deep: true
        }
    },
    computed: {
        activeList() {
            return this.note.list.filter(it => it.isCompleted === false);
        },
        completedList() {
            return this.note.list.filter(it => it.isCompleted === true);
        },
    },
    methods: {
        edit(note) {
            // let ix = this.itemList.indexOf(note);
            this.note = note;
        },
        plural: plural,
        changeType(type) {
            this.note.type = type;

            if (this.note.type === TYPE.list) {
                this.note.text = '';
            }

            if (this.note.type === TYPE.text) {
                this.note.list = [];
            }
        },
        add(e) {
            let text = e.target.value;
            e.target.value = '';
            this.note.list.push({
                text: text,
                isCompleted: false,
            });

            this.$nextTick(() => {
                let allInputs = document.querySelectorAll('#list input');
                let lastInput = allInputs[allInputs.length - 1];
                lastInput.focus();
            });
        },
        onCheckboxChange(e, value) {
            e.target.checked = value;
        },
        save() {
            // let note = {
            //     name: this.note.name,
            //     text: this.note.text,
            //     list: [...this.note.list],
            //     type: this.note.type,
            // }

            // let note = {...this.note};

            let note = JSON.parse(JSON.stringify(this.note));

            if (this.note.name === '') {
                return;
            }

            this.note.name = '';
            this.note.text = '';
            this.note.list = [];

            this.itemList.push(note);

            this.saveState();
        },
        saveState() {
            localStorage.setItem('data_todo-app-single-page-like-googlekeep', JSON.stringify({ itemList: this.itemList }))
        },
        loadState() {
            let data = localStorage.getItem('data_todo-app-single-page-like-googlekeep');
            if (data) {
                data = JSON.parse(data);
                this.itemList = data.itemList;
            }
        },
    },
}
</script>

<style>
body {
  background-color: azure;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  padding-top: 60px;
  color: #2c3e50;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
.vtc {
    text-overflow: ellipsis;
    overflow: hidden;
    max-height: 200px;
}
.lsn {
    list-style: none;
}
</style>