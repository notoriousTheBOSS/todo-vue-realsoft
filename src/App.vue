<script setup>
import { computed, onUnmounted, ref } from "vue";

const input = ref("");
const bugun = ref([]);
const ertaga = ref([]);
const keyin = ref([]);
const hourRegex = /[0-9][0-9]:[0-9][0-9]/;
const dateRegex = /\d{1,2}\.\d{1,2}\.\d{2,4}/;
const currentDATE = ref(new Date());

const interval = setInterval(() => {
    currentDATE.value = new Date();
}, 1000);

onUnmounted(() => {
    clearInterval(interval);
});

const addTodo = () => {
    if (input.value.trim().length > 0) {
        if (input.value.toLowerCase().split(" ").includes("bugun")) {
            let result = input.value.replace("bugun", "");
            if (result.length > 2) {
                const newTask = {
                    id: Date.now(),
                    text: result,
                    isCompleted: false,
                };
                bugun.value.push(newTask);
                localStorage.setItem("bugun", JSON.stringify(bugun.value));
            }
        } else if (input.value.toLowerCase().split(" ").includes("ertaga")) {
            let result = input.value.replace("ertaga", "");
            if (result.length > 2) {
                const newTask = {
                    id: Date.now(),
                    text: result,
                    isCompleted: false,
                };
                ertaga.value.push(newTask);
                localStorage.setItem("ertaga", JSON.stringify(ertaga.value));
            }
        } else if (input.value.toLowerCase().match(dateRegex)) {
            let result = input.value;
            if (result.length > 2) {
                const newTask = {
                    id: Date.now(),
                    text: result,
                    isCompleted: false,
                };
                keyin.value.push(newTask);
                localStorage.setItem("keyin", JSON.stringify(keyin.value));
            }
        } else {
            let result = input.value;
            if (result.length > 2) {
                const newTask = {
                    id: Date.now(),
                    text: result,
                    isCompleted: false,
                };
                bugun.value.push(newTask);
                localStorage.setItem("bugun", JSON.stringify(bugun.value));
            }
        }
        input.value = "";
    } else {
        alert("Maydonni to'ldiring");
    }
};

const storedBugun = localStorage.getItem("bugun");
const storedErtaga = localStorage.getItem("ertaga");
const storedKeyin = localStorage.getItem("keyin");

if (storedBugun) {
    bugun.value.push(...JSON.parse(storedBugun));
}
if (storedErtaga) {
    ertaga.value.push(...JSON.parse(storedErtaga));
}
if (storedKeyin) {
    keyin.value.push(...JSON.parse(storedKeyin));
}

const completeTodo = (item) => {
    const newArray = ref([]);
    if (item.text.includes("bugun")) {
        newArray.value = bugun.value.map((todo) =>
            todo.id == item.id
                ? { ...todo, isCompleted: !todo.isCompleted }
                : todo
        );
        bugun.value = newArray.value;
        localStorage.setItem("bugun", JSON.stringify(bugun.value));
    } else if (item.text.includes("ertaga")) {
        newArray.value = ertaga.value.map((todo) =>
            todo.id == item.id
                ? { ...todo, isCompleted: !todo.isCompleted }
                : todo
        );
        ertaga.value = newArray.value;
        localStorage.setItem("ertaga", JSON.stringify(ertaga.value));
    } else if (item.text.match(dateRegex)) {
        newArray.value = keyin.value.map((todo) =>
            todo.id == item.id
                ? { ...todo, isCompleted: !todo.isCompleted }
                : todo
        );
        keyin.value = newArray.value;
        localStorage.setItem("keyin", JSON.stringify(keyin.value));
    } else {
        newArray.value = bugun.value.map((todo) =>
            todo.id == item.id
                ? { ...todo, isCompleted: !todo.isCompleted }
                : todo
        );
        bugun.value = newArray.value;
        localStorage.setItem("bugun", JSON.stringify(bugun.value));
    }
};
const sortedBugun = computed(() => {
    return bugun.value.slice().sort((a, b) => {
        if (a.isCompleted !== b.isCompleted) {
            return a.isCompleted ? 1 : -1;
        }

        const aHour = a.text.match(hourRegex)
            ? parseInt(a.text.match(hourRegex)[0], 10)
            : new Date(a.id).getHours() + 1;

        const bHour = b.text.match(hourRegex)
            ? parseInt(b.text.match(hourRegex)[0], 10)
            : new Date(b.id).getHours() + 1;

        return aHour - bHour;
    });
});
const sortedErtaga = computed(() => {
    return ertaga.value.slice().sort((a, b) => {
        if (a.isCompleted !== b.isCompleted) {
            return a.isCompleted ? 1 : -1;
        }

        const aHour = a.text.match(hourRegex)
            ? parseInt(a.text.match(hourRegex)[0], 10)
            : new Date(a.id).getHours() + 1;

        const bHour = b.text.match(hourRegex)
            ? parseInt(b.text.match(hourRegex)[0], 10)
            : new Date(b.id).getHours() + 1;

        return aHour - bHour;
    });
});
const sortedKeyin = computed(() => {
    return keyin.value.slice().sort((a, b) => {
        if (a.isCompleted !== b.isCompleted) {
            return a.isCompleted ? 1 : -1;
        }

        const aDate = new Date(a.text.match(dateRegex)[0]);
        const bDate = new Date(b.text.match(dateRegex)[0]);

        if (aDate < bDate) return -1;
        if (aDate > bDate) return 1;

        const aHour = a.text.match(hourRegex)
            ? parseInt(a.text.match(hourRegex)[0], 10)
            : new Date(a.id).getHours() + 1;

        const bHour = b.text.match(hourRegex)
            ? parseInt(b.text.match(hourRegex)[0], 10)
            : new Date(b.id).getHours() + 1;

        return aHour - bHour;
    });
});
</script>

<template setup>
    <section>
        <div class="container">
            <div
                class="w-full py-10 border rounded-md px-5 min-h-screen h-auto flex flex-col justify-between"
            >
                <div>
                    <h1 class="text-center mt-[20px] text-[38px] font-bold">
                        Vazifalar Menedjeri
                    </h1>
                    <div>
                        <div
                            class="flex items-center justify-between max-w-full gap-[25px] w-full mt-[100px]"
                        >
                            <input
                                type="text"
                                placeholder="Yangi vazifa qo'shish"
                                class="outline-none border-[3px] border-gray-300 max-w-[540px] w-full h-[50px] p-4 rounded-md"
                                v-model="input"
                            />
                            <button
                                @click="addTodo"
                                class="bg-green-300 border-[3px] rounded-md border-green-400 w-[50px] h-[50px] grid place-content-center"
                            >
                                <svg
                                    xmlns="http://www.w3.org/2000/svg"
                                    width="20"
                                    height="20"
                                    fill="currentColor"
                                    class="bi bi-plus"
                                    viewBox="0 0 16 16"
                                >
                                    <path
                                        d="M8 4a.5.5 0 0 1 .5.5v3h3a.5.5 0 0 1 0 1h-3v3a.5.5 0 0 1-1 0v-3h-3a.5.5 0 0 1 0-1h3v-3A.5.5 0 0 1 8 4z"
                                    />
                                </svg>
                            </button>
                        </div>
                        <span class="text-gray-500 text-[17px] mt-[10px]">
                            Bugun: {{ currentDATE.toLocaleString() }}
                        </span>
                    </div>
                    <div>
                        <h2 class="text-[30px] font-bold mt-[40px] mb-[7px]">
                            Bugun
                        </h2>
                        <ul
                            v-if="sortedBugun.length"
                            class="px-[10px] flex flex-col gap-y-[15px]"
                        >
                            <li
                                v-for="(item, index) in sortedBugun"
                                :key="index"
                                class="max-w-full w-full flex items-center justify-between"
                            >
                                <span class="flex gap-x-[20px]">
                                    <span
                                        @click="completeTodo(item)"
                                        class="w-[25px] h-[25px] rounded-md"
                                        :class="
                                            item.isCompleted
                                                ? 'bg-blue-400'
                                                : 'border-[3px] border-gray-300'
                                        "
                                    ></span>
                                    <p
                                        class="font-medium text-[17px] text-gray-600"
                                        :class="
                                            item.isCompleted
                                                ? `line-through`
                                                : ``
                                        "
                                    >
                                        {{
                                            item.text.replace(
                                                item.text.match(hourRegex),
                                                ""
                                            )
                                        }}
                                    </p>
                                </span>
                                <p
                                    class="font-medium text-[17px] text-gray-600"
                                    :class="
                                        item.isCompleted ? `line-through` : ``
                                    "
                                >
                                    {{
                                        item.text.match(hourRegex)
                                            ? item.text.match(hourRegex)[0]
                                            : new Date(item.id).getHours() +
                                              1 +
                                              ":00"
                                    }}
                                </p>
                            </li>
                        </ul>
                        <p v-else class="font-medium text-[17px] text-gray-400">
                            Hozircha ma'lumot yo'q...
                        </p>
                    </div>
                    <div>
                        <h2 class="text-[30px] font-bold mt-[40px] mb-[7px]">
                            Ertaga
                        </h2>
                        <ul
                            v-if="sortedErtaga.length"
                            class="px-[10px] flex flex-col gap-y-[15px]"
                        >
                            <li
                                v-for="(item, index) in sortedErtaga"
                                :key="index"
                                class="max-w-full w-full flex items-center justify-between"
                            >
                                <span class="flex gap-x-[20px]">
                                    <span
                                        @click="completeTodo(item)"
                                        class="w-[25px] h-[25px] rounded-md"
                                        :class="
                                            item.isCompleted
                                                ? 'bg-blue-400'
                                                : 'border-[3px] border-gray-300'
                                        "
                                    ></span>
                                    <p
                                        class="font-medium text-[17px] text-gray-600"
                                        :class="
                                            item.isCompleted
                                                ? `line-through`
                                                : ``
                                        "
                                    >
                                        {{
                                            item.text.replace(
                                                item.text.match(hourRegex),
                                                ""
                                            )
                                        }}
                                    </p>
                                </span>
                                <p
                                    class="font-medium text-[17px] text-gray-600"
                                    :class="
                                        item.isCompleted ? `line-through` : ``
                                    "
                                >
                                    {{
                                        item.text.match(hourRegex)
                                            ? item.text.match(hourRegex)[0]
                                            : new Date(item.id).getHours() +
                                              1 +
                                              ":00"
                                    }}
                                </p>
                            </li>
                        </ul>
                        <p v-else class="font-medium text-[17px] text-gray-400">
                            Hozircha ma'lumot yo'q...
                        </p>
                    </div>
                    <div>
                        <h2 class="text-[30px] font-bold mt-[40px] mb-[7px]">
                            Keyin
                        </h2>
                        <ul
                            v-if="sortedKeyin.length"
                            class="px-[10px] flex flex-col gap-y-[15px]"
                        >
                            <li
                                v-for="(item, index) in sortedKeyin"
                                :key="index"
                                class="max-w-full w-full flex items-center justify-between"
                            >
                                <span class="flex gap-x-[20px]">
                                    <span
                                        @click="completeTodo(item)"
                                        class="w-[25px] h-[25px] rounded-md"
                                        :class="
                                            item.isCompleted
                                                ? 'bg-blue-400'
                                                : 'border-[3px] border-gray-300'
                                        "
                                    ></span>
                                    <p
                                        class="font-medium text-[17px] text-gray-600"
                                        :class="
                                            item.isCompleted
                                                ? `line-through`
                                                : ``
                                        "
                                    >
                                        {{
                                            item.text
                                                .replace(
                                                    item.text.match(dateRegex),
                                                    ""
                                                )
                                                .replace(
                                                    item.text.match(hourRegex),
                                                    ""
                                                )
                                        }}
                                    </p>
                                </span>
                                <p
                                    class="font-medium text-[17px] text-gray-600"
                                    :class="
                                        item.isCompleted ? `line-through` : ``
                                    "
                                >
                                    {{ item.text.match(dateRegex)[0] }},
                                    {{
                                        item.text.match(hourRegex)
                                            ? item.text.match(hourRegex)[0]
                                            : new Date(item.id).getHours() +
                                              1 +
                                              ":00"
                                    }}
                                </p>
                            </li>
                        </ul>
                        <p v-else class="font-medium text-[17px] text-gray-400">
                            Hozircha ma'lumot yo'q...
                        </p>
                    </div>
                </div>
                <div
                    className="flex items-center justify-between mt-[70px] text-[13px] text-gray-400"
                >
                    <span></span>
                    <span className="flex items-end flex-col">
                        <p>
                            Bajarilganlar:{{
                                [...bugun, ...ertaga, ...keyin].filter(
                                    (v) => v.isCompleted === true
                                ).length
                            }}
                        </p>
                        <p>
                            Bajarilmaganlar:{{
                                [...bugun, ...ertaga, ...keyin].filter(
                                    (v) => v.isCompleted === false
                                ).length
                            }}
                        </p>
                    </span>
                </div>
            </div>
        </div>
    </section>
</template>
