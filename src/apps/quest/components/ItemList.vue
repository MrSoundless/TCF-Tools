<template>
    <section class="quarter-line__selector">
        <div @click="currentSlide(1)" :class="{'active-slide': slideIndex === 1}" class="quarter-line__selector-tab">
            <img src="/quest-images/Quarters/AllItems.png" class="modal__header-image" />
            <span> All Items </span>
            <img src="/quest-images/Quarters/AllItems.png" class="modal__header-image" />
        </div>

        <div @click="currentSlide(2)" :class="{'active-slide': slideIndex === 2}" class="quarter-line__selector-tab">
            <img src="/quest-images/Quarters/AllMissions.png" class="modal__header-image" />
            <span> Mission Items </span>
            <img src="/quest-images/Quarters/AllMissions.png" class="modal__header-image" />
        </div>

        <div @click="currentSlide(3)" :class="{'active-slide': slideIndex === 3}" class="quarter-line__selector-tab">
            <img src="/quest-images/Quarters/QuarterLevel.png" class="modal__header-image" />
            <span> Quarter Items </span>
            <img src="/quest-images/Quarters/QuarterLevel.png" class="modal__header-image" />
        </div>
    </section>

    <div class="item-list__container-inner fade">
        <p v-if="Object.keys(currentItems).length > 0">Complete missions and upgrades to update the list</p>
        <p v-else class="complete">You have completed every mission and upgrade.</p>

        <div class="search-container" v-if="Object.keys(currentItems).length > 0">
            <input type="text" v-model="searchValue" placeholder="Search for items..." />
        </div>

        <div class="search-options-container">
            <input class="current-progress-only" type="checkbox" v-model="onlyShowCurrentProgress" @change="onlyShowCurrentProgressChanged()" id="current-mission-items-only" />
            <label for="current-mission-items-only">Only show current levels</label>

            <input class="main-missions-only" type="checkbox" v-model="onlyShowMainMissions" @change="onlyShowMainMissionsChanged()" id="main-mission-items-only" />
            <label for="main-mission-items-only">Hide side missions</label>
        </div>

        <section class="list__container">
            <div v-for="(amount, index) in currentItems" :key="index.toString()" class="item__row" :class="{matching: rowColor(index.toString())}">
                <img :src="'/map-images/item-images/' + itemName(index.toString(), true) + '.png'" class="item__image" />
                <span>
                    <span v-if="index.toString() == 'SoftCurrency'"> {{ amount / 1000 }}k </span>
                    <span v-else> {{ amount }}</span>
                    {{ itemName(index.toString()) }}
                </span>
            </div>
        </section>
    </div>

    <div class="item-list__container-inner fade">
        <p v-if="Object.keys(currentMissionsItems).length > 0">Complete missions to update the list</p>
        <p v-else class="complete">You have completed every mission.</p>

        <div class="search-container" v-if="Object.keys(currentMissionsItems).length > 0">
            <input type="text" v-model="searchValue" placeholder="Search for items..." />
        </div>
  
        <div class="search-options-container">
            <input class="current-progress-only" type="checkbox" v-model="onlyShowCurrentProgress" @change="onlyShowCurrentProgressChanged()" id="current-mission-items-only" />
            <label for="current-mission-items-only">Only show current levels</label>
            <input class="main-missions-only" type="checkbox" v-model="onlyShowMainMissions" @change="onlyShowMainMissionsChanged()" id="main-mission-items-only" />
            <label for="main-mission-items-only">Hide side missions</label>
        </div>

        <section class="list__container">
            <div v-for="(amount, index) in currentMissionsItems" :key="index.toString()" class="item__row" :class="{matching: rowColor(index.toString())}">
                <img :src="'/map-images/item-images/' + itemName(index.toString(), true) + '.png'" class="item__image" />
                <span>
                    <span v-if="index.toString() == 'SoftCurrency'"> {{ amount / 1000 }}k </span>
                    <span v-else> {{ amount }}</span>
                    {{ itemName(index.toString()) }}
                </span>
            </div>
        </section>
    </div>

    <div class="item-list__container-inner fade">
        <p v-if="Object.keys(currentQuarterItems).length > 0">Complete upgrades to update the list</p>
        <p v-else class="complete">You have completed every upgrade.</p>

        <div class="search-container" v-if="Object.keys(currentQuarterItems).length > 0">
            <input type="text" v-model="searchValue" placeholder="Search for items..." />
        </div>
        <div class="search-options-container">
            <input class="current-progress-only" type="checkbox" v-model="onlyShowCurrentProgress" @change="onlyShowCurrentProgressChanged()" id="current-quarter-items-only" />
            <label for="current-quarter-items-only">Only show current levels</label>
        </div>

        <section class="list__container">
            <div v-for="(amount, index) in currentQuarterItems" :key="index.toString()" class="item__row" :class="{matching: rowColor(index.toString())}">
                <img :src="'/map-images/item-images/' + itemName(index.toString(), true) + '.png'" class="item__image" />
                <span>
                    <span v-if="index.toString() == 'SoftCurrency'"> {{ amount / 1000 }}k </span>
                    <span v-else> {{ amount }}</span>
                    {{ itemName(index.toString()) }}
                </span>
            </div>
        </section>
    </div>
</template>

<script lang="ts">
import {keyCardInfo} from "../../map/mapConstants";
import {defineComponent} from "vue";
import {POSITION, useToast} from "vue-toastification";
import {missionData, missionListData, stringTables, techLevelsData, techTreeData} from "../data";
import {factionProgress, quarterProgress} from "../trackProgress";

import {getItemsOfMission, itemName} from "../utils";

export default defineComponent({
    data() {
        return {
            progressData: factionProgress,
            quarterProgress: quarterProgress,

            currentMissionsItems: {} as any,
            previousMissionItems: {} as any,

            currentQuarterItems: {} as any,
            previousQuarterItems: {} as any,
            onlyShowCurrentProgress: false,
            onlyShowMainMissions: false,

            currentItems: {} as any,
            previousItems: {} as any,
            keycardInfo: keyCardInfo,
            stringTables: stringTables["Materials"],
            toast: useToast(),

            techLevelsData: techLevelsData,
            techTreeData: techTreeData,

            slideIndex: 1,

            searchValue: "",
        };
    },
    mounted() {
        this.getIncompleteMissionItems();
        this.getQuarterItems();
        this.updateList();

        this.showSlides(this.slideIndex);
    },
    methods: {
        rowColor(name: string): boolean {
            if (this.searchValue.length < 3) return false;
            const itemName = this.itemName(name);

            if (itemName.toLowerCase().includes(this.searchValue.toLowerCase())) return true;

            return false;
        },
        plusSlides(n: number) {
            this.showSlides((this.slideIndex += n));
        },
        currentSlide(n: number) {
            this.showSlides((this.slideIndex = n));
        },
        showSlides(n: number) {
            let i;
            let slides = document.getElementsByClassName("item-list__container-inner") as any;
            if (n > slides.length) {
                this.slideIndex = 1;
            }
            if (n < 1) {
                this.slideIndex = slides.length;
            }
            for (i = 0; i < slides.length; i++) {
                slides[i].style.display = "none";
            }

            slides[this.slideIndex - 1].style.display = "block";
        },
        onlyShowCurrentProgressChanged(): void {
            this.getIncompleteMissionItems();
            this.getQuarterItems();
        },
        onlyShowMainMissionsChanged(): void {
            this.getIncompleteMissionItems();
        },
        getIncompleteMissionItems(): void {
            let newData: any = {};
            for (let name in missionListData) {
                const questLine = missionListData[name];
                const missions = questLine['missions'];
                const length = missions.length;
                const progress = this.progressData.get()[name];
                if (this.onlyShowMainMissions && !questLine.isMain)
                    continue;

                if (this.onlyShowCurrentProgress && progress == 0 && questLine.hasOwnProperty('dependsOnMission')) {
                    const requiredQuestName = questLine.dependsOnMission;
                    const requiredQuest = missionData[requiredQuestName];

                    if (this.progressData.get()[requiredQuest.chainName] < requiredQuest.chainPart) {
                        // Don't count in the current questline because the dependant questline is not even finished yet
                        continue;
                    }
                }

                if (progress >= length) {
                    // we have already completed this mission
                } else {
                    // we have yet to complete this mission in full, so we get the data
                    for (let p in missions) {
                        const part = missions[p];

                        // we have not completed this part of the mission yet
                        if (progress < parseInt(p) + 1) {
                            const partData = missionData[part];

                            // just in case, if the data exists
                            if (partData) {
                                // get the total items we use for this mission
                                let data = getItemsOfMission(partData["objectives"]);

                                // save the items, adding duplicate entries together
                                for (let item in data) {
                                    newData[item] = newData[item]
                                        ? newData[item] + data[item]
                                        : data[item];
                                }
                                
                                if (this.onlyShowCurrentProgress) break;
                            }
                        }
                    }
                }
            
            }

            // sort our items by value, https://stackoverflow.com/questions/1069666/sorting-object-property-by-values#comment130783332_16794116
            const sortedData = Object.keys(newData)
                .sort((a, b) => newData[b] - newData[a])
                .reduce((r, k) => ({...r, [k]: newData[k]}), {});

            // save it
            this.previousMissionItems = {...this.currentMissionsItems};
            this.currentMissionsItems = sortedData;
            this.updateAllItemList();
        },
        getQuarterItems(): void {
            let newData: any = {};
            let quarterProgress = this.quarterProgress.get();
            let overalQuarterProgress = quarterProgress["overall"];

            // Overall quarter level
            let index = 0;
            for (let level in techLevelsData) {
                index += 1;

                if (overalQuarterProgress < index) {
                    for (let i in techLevelsData[level]["costs"]) {
                        const item = i;
                        const amount = techLevelsData[level]["costs"][i];

                        newData[item] = newData[item]
                            ? newData[item] + amount
                            : amount;
                    }
                    if (this.onlyShowCurrentProgress) break;
                }
            }

            // tech tree levels
            for (let upgrade in techTreeData) {
                const levels = techTreeData[upgrade]["levels"];
                const pqLevelRequired = techTreeData[upgrade]["PQLevelRequired"];
                const progress = quarterProgress[upgrade];
                if (upgrade >= levels.length || (this.onlyShowCurrentProgress && overalQuarterProgress < pqLevelRequired)) continue;

                for (let l in levels) {
                    // we have not completed this upgrade yet
                    if (progress < parseInt(l) + 1) {
                        for (let i in levels[l]["costs"]) {
                            const item = i;
                            const amount = techTreeData[upgrade]["levels"][l]["costs"][i];

                            if (newData[item]) {
                                newData[item] = newData[item] + amount;
                            } else {
                                newData[item] = amount;
                            }
                        }
                        if (this.onlyShowCurrentProgress) break;
                    }
                }
            }

            const sortedData = Object.keys(newData)
                .sort((a, b) => newData[b] - newData[a])
                .reduce((r, k) => ({...r, [k]: newData[k]}), {});

            // save it
            this.previousQuarterItems = {...this.previousQuarterItems};
            this.currentQuarterItems = sortedData;
            this.updateAllItemList();
        },
        sendNotification(): void {
            // send out a function to inform the user that the item list changed when they change if they finished a mission or not

            // JS compares objects by reference type, so two objects with the same
            // keys and values are always different. Therefore we convert to a string to check if before is same as after.
            // The order of the object is always the same.
            // https://stackoverflow.com/questions/11704971/why-are-two-identical-objects-not-equal-to-each-other
            const before = JSON.stringify(this.previousItems);
            const after = JSON.stringify(this.currentItems);

            if (before === after) return;
            this.toast.info("Item list updated", {timeout: 2000, position: POSITION.BOTTOM_RIGHT});
        },
        updateAllItemList() {
            let bigList: any = {};

            for (let item in this.currentMissionsItems) {
                const amount = this.currentMissionsItems[item];
                bigList[item] = bigList[item]
                    ? bigList[item] + amount
                    : amount;
            }

            for (let item in this.currentQuarterItems) {
                const amount = this.currentQuarterItems[item];
                bigList[item] = bigList[item]
                    ? bigList[item] + amount
                    : amount;
            }

            const sortedData = Object.keys(bigList)
                .sort((a, b) => bigList[b] - bigList[a])
                .reduce((r, k) => ({...r, [k]: bigList[k]}), {});

            this.previousItems = {...this.currentItems};
            this.currentItems = sortedData;
        },
        updateList() {
            this.updateAllItemList();
            this.sendNotification();
        },
        itemName(item: string, urlFormat?: boolean): string {
            return itemName(item, urlFormat);
        },
    },
    watch: {
        progressData: {
            deep: true,
            handler() {
                this.getIncompleteMissionItems();
                this.updateList();
            },
        },
        quarterProgress: {
            deep: true,
            handler() {
                this.getQuarterItems();
                this.updateList();
            },
        },
    },
});
</script>

<style scoped>
input[type=checkbox] {
    margin: auto 10px auto 20px;
}

p {
    text-align: center;
    font-size: var(--space-xl);
    margin-bottom: 0.3rem;
}

p.complete {
    border-bottom: none;
    margin-bottom: none;
}
.list__container {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 1fr;
    gap: var(--space-md);
}

.item__row {
    display: grid;
    grid-template-columns: 1fr 5fr;
    gap: 0.5rem;

    height: var(--space-xl);
}

.item__row span {
    text-overflow: ellipsis;
    display: flex;
    align-items: center;
    gap: var(--space-xs);
}

.item__image {
    height: var(--space-xl);
}

@media screen and (max-width: 1500px) {
    .list__container {
        grid-template-columns: 1fr 1fr 1fr;
    }
}
@media screen and (max-width: 1200px) {
    .list__container {
        grid-template-columns: 1fr 1fr;
    }
}
@media screen and (max-width: 900px) {
    .list__container {
        grid-template-columns: 1fr;
        margin: auto;
    }
}

.comment {
    text-align: center;
}

.fade {
    animation-name: fade-in;
    animation-duration: 1s;
    animation-timing-function: cubic-bezier(0.23, 1, 0.32, 1);
}

@keyframes fade-in {
    from {
        opacity: 0;
        translate: 0 30%;
    }
    to {
        opacity: 1;
        translate: 0 0;
    }
}

.quarter-line__selector {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    width: 100%;
    text-align: center;

    margin: 0 0 var(--space-md);

    cursor: pointer;
    border-collapse: collapse;
}

.quarter-line__selector div {
    text-transform: uppercase;
    letter-spacing: 0.4rem;

    padding: 0.5rem;

    position: relative;
    border-collapse: collapse;
    border: 1px solid var(--border-color-base);
}

@media screen and (max-width: 900px) {
    .quarter-line__selector {
        grid-template-columns: 1fr 1fr;
    }
}
.quarter-line__selector-tab {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    text-align: center;
    gap: 1rem;
}

.quarter-line__selector-tab span {
    text-align: center;
}
.quarter-line__selector-tab img {
    width: 2rem;
}

@media screen and (max-width: 900px) {
    .quarter-line__selector-tab img {
        display: none;
    }
}

.quarter-line__selector div:not(.active-slide)::before {
    content: "";
    position: absolute;
    top: 3px;
    left: 0;
    width: 100%;
    height: 100%;
    border-bottom: 2px solid transparent;

    transition: 0.4s ease-in-out;
}

.quarter-line__selector div:hover:not(.active-slide)::before {
    border-bottom-color: var(--color-base--subtle);
}
.active-slide::before:hover {
    border-bottom-color: var(--color-base--subtle) !important;
}

.active-slide::before {
    content: "";
    position: absolute;
    top: 3px;
    left: 0;
    width: 100%;
    height: 100%;
    border-bottom: 2px solid var(--color-base--subtle);

    transition: 0.4s ease-in-out;
}

.active-slide:hover::before {
    border-bottom-color: var(--color-base--subtle);
}

.matching {
    transition: all 0.2s linear;
    background-color: var(--color-surface-1);
    color: var(--color-base--subtle);
}

.search-container {
    width: 100%;
    display: flex;

    justify-content: center;
    align-items: center;

    padding-bottom: 1rem;
}

.search-container input {
    width: 30%;
    height: 100%;
    font-size: 1.2rem;
    padding: 0.2rem;
}

.search-options-container {
    width: 100%;
    display: flex;

    justify-content: center;
    align-items: center;

    padding-bottom: 1rem;
    margin-bottom: 1rem;

    border-bottom: 1px solid var(--border-color-base);
}
</style>
