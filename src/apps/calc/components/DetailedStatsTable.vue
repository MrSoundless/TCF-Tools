<template>
<div class="wrapper"> 
    <h2> Detailed Stats </h2>
    <EasyDataTable
        :headers="headersDetailed"
        :items="detailedData"
        table-class-name="customize-table"
        header-text-direction="center"
        body-text-direction="center"
        alternating
        :no-hover="true"
        :hide-footer="true"
    >
        <template #item-inGameName="{inGameName, weapon}">
            <div class="name-wrapper" v-html="weaponImage(inGameName, weapon)">

            </div>
        </template>
        <template #item-options="{weapon}">
            <div class="options-wrapper">
                <BodyChart :weapon="weapon" />
                <AttachmentSelector :weapon="weapon" />
            </div>
        </template>
    </EasyDataTable>
</div>
<div class="shots-container" v-if="selectedTarget.selected == 'PlayerDefault'">
    <div class="wrapper wrapper-small"> 
        <h2> Bodyshots to Kill </h2>
        <EasyDataTable
            :headers="headersShotsToKillBody"
            :items="detailedData"
            table-class-name="customize-table"
            header-text-direction="center"
            body-text-direction="center"
            alternating
            :no-hover="true"
            :hide-footer="true"
        >
            <template #header-Shield_01="header">
                <img 
                    class="table-header-image"
                    :src="'map-images/item-images/Basic_Shields.png'" 
                />
            </template>
            <template #header-Shield_02="header">
                <img 
                    class="table-header-image"
                    :src="'map-images/item-images/Standard_Shields.png'" 
                />
            </template>
            <template #header-Shield_03="header">
                <img 
                    class="table-header-image"
                    :src="'map-images/item-images/Reinforced_Shields.png'" 
                />
            </template>
            <template #header-Shield_04="header">
                <img 
                    class="table-header-image"
                    :src="'map-images/item-images/Combat_Shields.png'" 
                />
            </template>
            <template #header-Shield_05="header">
                <img 
                    class="table-header-image"
                    :src="'map-images/item-images/Enhanced_Shields.png'" 
                />
            </template>
            <template #header-Shield_Altered_03="header">
                <img 
                    class="table-header-image"
                    :src="'map-images/item-images/Forged_Shield.png'" 
                />
            </template>
            <template #item-inGameName="{inGameName, weapon}">
                <div class="name-wrapper" v-html="weaponImage(inGameName, weapon)">
                </div>
            </template>
            <template #item-options="{weapon}">
                <div class="options-wrapper">
                    <BodyChart :weapon="weapon" />
                    <AttachmentSelector :weapon="weapon" />
                </div>
            </template>
        </EasyDataTable>
    </div>
    <div class="wrapper wrapper-small"> 
        <h2> Headshots to Kill </h2>
        <EasyDataTable
            :headers="headersShotsToKillHead"
            :items="detailedData"
            table-class-name="customize-table"
            header-text-direction="center"
            body-text-direction="center"
            alternating
            :no-hover="true"
            :hide-footer="true"
        >
            <template #header-Helmet_01="header">
                <img 
                    class="table-header-image"
                    :src="'map-images/item-images/Basic_Helmet.png'" 
                />
            </template>
            <template #header-Helmet_02="header">
                <img 
                    class="table-header-image"
                    :src="'map-images/item-images/Standard_Helmet.png'" 
                />
            </template>
            <template #header-Helmet_03="header">
                <img 
                    class="table-header-image"
                    :src="'map-images/item-images/Reinforced_Helmet.png'" 
                />
            </template>
            <template #header-Helmet_04="header">
                <img 
                    class="table-header-image"
                    :src="'map-images/item-images/Combat_Helmet.png'" 
                />
            </template>
            <template #header-Helmet_05="header">
                <img 
                    class="table-header-image"
                    :src="'map-images/item-images/Enhanced_Helmet.png'" 
                />
            </template>
            <template #header-Helmet_Altered_03="header">
                <img 
                    class="table-header-image"
                    :src="'map-images/item-images/Legendary_NV_Helmet.png'" 
                />
            </template>
            <template #item-inGameName="{inGameName, weapon}">
                <div class="name-wrapper" v-html="weaponImage(inGameName, weapon)">
                </div>
            </template>
            <template #item-options="{weapon}">
                <div class="options-wrapper">
                    <BodyChart :weapon="weapon" />
                    <AttachmentSelector :weapon="weapon" />
                </div>
            </template>
        </EasyDataTable>
    </div>
</div>
<div class="wrapper"> 
    <h2> Weapon Stats </h2>
    <EasyDataTable
        :headers="headersStats"
        :items="detailedData"
        table-class-name="customize-table"
        header-text-direction="center"
        body-text-direction="center"
        alternating
        :no-hover="true"
        :hide-footer="true"
    >
        <template #item-inGameName="{inGameName, weapon}">
            <div class="name-wrapper" v-html="weaponImage(inGameName, weapon)">
            </div>
        </template>
        <template #item-options="{weapon}">
            <div class="options-wrapper">
                <BodyChart :weapon="weapon" />
                <AttachmentSelector :weapon="weapon" />
            </div>
        </template>
    </EasyDataTable>
</div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import {selectedWeapons, selectedArmor, selectedTarget, selectedHSValue, selectedDistance, selectedAttachments, selectedWeakspotValue, selectedAccuracy, updateGlobalStateUtility } from "../store";
import { calculate, setCurrentWeapon, updateRunTimeSettings } from "../calculate";
import type { Item } from "vue3-easy-data-table";
import { roundToThree } from "../utils";
import { weaponData, armorData, itemData } from "../data";
import BodyChart from "./BodyChart.vue";
import AttachmentSelector from "./AttachmentSelector.vue";
import { helmetData } from "@/apps/forge/data";
import { penetrationChart, falloffChart, ttkChart, stkChart } from "../charts";
import { setRunTimeSettings } from "../calculate";
import { setRunTimeSettingsArmor } from "../calculate";

export default defineComponent({
    components: {
        BodyChart,
        AttachmentSelector
    },
    data() {
        return {
            selectedWeapons,
            selectedArmor,
            selectedTarget,
            selectedHSValue,
            selectedDistance,
            selectedAttachments,
            selectedWeakspotValue,
            selectedAccuracy,
            updateGlobalStateUtility,
            headersDetailed: [
                { text: "Weapon", value: "inGameName", sortable: true, width: 200, fixed: true },
                { text: 'Options', value: 'options'},
                { text: "Penetration Multiplier", value: "penMult", sortable: true },
                { text: "Rounds per Minute", value: "rpm", sortable: true },
                { text: "Reload Adjust RPM", value: "reloadRPM", sortable: true },
                { text: "Damage per Second (Approx)", value: "dps", sortable: true},
                { text: "Reload Adjust DPS", value: "reloadDPS", sortable: true},
                { text: "Damage per Mag", value: "dpm", sortable: true},
                { text: "Time to Empty Mag (s)", value: "timeToEmpty", sortable: true},
                { text: "Shots to Kill", value: "stk", sortable: true},
                { text: "Time to Kill", value: "ttk", sortable: true},
                { text: "Damage per Bullet", value: "dmgPerBull", sortable: true},
                { text: "Damage per Bullet (HS)", value: "dmgPerBullHS", sortable: true},
            ],
            headersShotsToKillBody: [
                { text: "Weapon", value: "inGameName", sortable: true, width: 200, fixed: true },
                { text: "No Armor", value: "PlayerDefault", sortable: true },
                { text: "Common", value: "Shield_01", sortable: true },
                { text: "Uncommon", value: "Shield_02", sortable: true },
                { text: "Rare", value: "Shield_03", sortable: true },
                { text: "Epic", value: "Shield_04", sortable: true },
                { text: "Exotic", value: "Shield_05", sortable: true },
                { text: "Legendary", value: "Shield_Altered_03", sortable: true },
                // { text: 'Options', value: 'options'}
            ],
            headersShotsToKillHead: [
                { text: "Weapon", value: "inGameName", sortable: true, width: 200, fixed: true },
                { text: "No Armor", value: "PlayerDefaultHS", sortable: true },
                { text: "Common", value: "Helmet_01", sortable: true },
                { text: "Uncommon", value: "Helmet_02", sortable: true },
                { text: "Rare", value: "Helmet_03", sortable: true },
                { text: "Epic", value: "Helmet_04", sortable: true },
                { text: "Exotic", value: "Helmet_05", sortable: true },
                { text: "Legendary", value: "Helmet_Altered_03", sortable: true },
                // { text: 'Options', value: 'options'}
            ],
            headersStats: [
                {text: "Weapon", value: "inGameName", sortable: true, width: 200, fixed: true},
                {text: 'Rarity', value: 'rarity', sortable: true}, 
                {text: 'Direct Damage', value: 'directDamage', sortable: true}, 
                {text: 'Penetration', value: 'penetration', sortable: true}, 
                {text: 'Explosive Damage', value: 'radialDamage', sortable: true}, 
                {text: 'Refire Time (s)', value: 'refireTime', sortable: true}, 
                {text: 'Charge Up Time (s)', value: 'spinupTime', sortable: true}, 
                {text: 'Reload Time (s)', value: 'reloadTime', sortable: true}, 
                {text: 'Magazine Size', value: 'ammoInClip', sortable: true}, 
                // {text: 'Ammo Type', value: 'ammoType', sortable: true}, 
                {text: 'Ammo Consumed Per Bullet', value: 'ammoPerBullet', sortable: true}, 
                {text: 'Projectile Speed (cm/s)', value: 'projectileSpeed', sortable: true}, 
                {text: 'Additional Burst Bullets', value: 'amountOfBurst', sortable: true}, 
                {text: 'Time between Burst Bullets (s)', value: 'burstInterval', sortable: true}, 
                {text: 'Shotgun Pellet Amount', value: 'amountOfImmediateFires', sortable: true}, 
                {text: 'Movement Speed Multiplier', value: 'movementSpeedMultiplier', sortable: true}, 
                {text: 'Unequip Time (s)', value: 'unequipTime', sortable: true}, 
                {text: 'Equip Time (s)', value: 'equipTime', sortable: true}, 
                {text: 'Player Headshot Multiplier', value: 'weakDamageMultiplier', sortable: true}, 
                {text: 'Falloff Range End (cm)', value: 'FalloffEnd', sortable: true}, 
                {text: 'Falloff Multiplier', value: 'FalloffMultiplier', sortable: true}, 
                {text: 'Falloff Range Start (cm)', value: 'FalloffStart', sortable: true}, 
                // {text: 'Weapon Type', value: 'tags', sortable: true}, 
                {text: 'Price', value: 'buyValue', sortable: true}, 
                // {text: 'Sell Price', value: 'sellValue', sortable: true}, 
                // {text: 'Faction', value: 'faction', sortable: true}, 
                // {text: 'Faction Reputation', value: 'factionRep', sortable: true}, 
                // {text: 'Weight', value: 'weight', sortable: true},
                {text: 'Audible Range (m)', value: 'audibleRange', sortable: true},
                // {text: 'Options', value: 'options'}
            ],
            validStats: [] as string[],
            detailedData: [] as Item,
            shotsToKillHeaderNames: [
                "PlayerDefault",
                "Shield_01",
                "Shield_02",
                "Shield_03",
                "Shield_04",
                "Shield_05",
                "Shield_Altered_03"
            ],
            shotsToKillHeaderNamesHead: [
                "PlayerDefaultHS",
                "Helmet_01",
                "Helmet_02",
                "Helmet_03",
                "Helmet_04",
                "Helmet_05",
                "Helmet_Altered_03"
            ]
        }
    },
    methods: {
        updateDetailedStats() {
            updateRunTimeSettings()

            let armorValues = [
                armorData["PlayerDefault"]["armorAmount"],
                armorData["Shield_01"]["armorAmount"],
                armorData["Shield_02"]["armorAmount"],
                armorData["Shield_03"]["armorAmount"],
                armorData["Shield_04"]["armorAmount"],
                armorData["Shield_05"]["armorAmount"],
                armorData["Shield_Altered_03"]["armorAmount"]
            ]

            const data : Item = [];
            for (let wep in selectedWeapons.list) {
                let wepName = selectedWeapons.list[wep];
                setCurrentWeapon(wepName)
                setRunTimeSettings("default")
                let wepStats : any = {
                    "weapon": wepName,
                    "inGameName":      weaponData[wepName]['inGameName'],
                    "penMult":     roundToThree(calculate.penetrationMultiplier()),
                    "rpm":         Math.round(calculate.roundsPerMinute()),
                    "reloadRPM":   Math.round(calculate.roundsPerMinuteReloadAdjusted()),
                    "dps":         Math.round(calculate.damagePerSecond()),
                    "reloadDPS":   Math.round(calculate.damagePerSecondReloadAdjusted()),
                    "dpm":         Math.round(calculate.damagePerMag()),
                    "timeToEmpty": roundToThree(calculate.totalTimeToEmptyMag()),
                    "stk":         Math.round(calculate.shotsToKill()),
                    "ttk":         roundToThree(calculate.timeToKill()),
                    "dmgPerBull":  roundToThree(calculate.damageOnBodyShot()),
                    "dmgPerBullHS":roundToThree(calculate.damageOnWeakSpotShot()),
                }

                setRunTimeSettings("shotsToKillTableBody")
                for (let shield in this.shotsToKillHeaderNames) {
                    setRunTimeSettingsArmor(armorValues[shield])
                    wepStats[this.shotsToKillHeaderNames[shield]] = calculate.shotsToKill()
                }

                setRunTimeSettings("shotsToKillTableHead")
                for (let shield in this.shotsToKillHeaderNamesHead) {
                    setRunTimeSettingsArmor(armorValues[shield])
                    wepStats[this.shotsToKillHeaderNamesHead[shield]] = calculate.shotsToKill()
                }

                for (let stat in this.validStats) {
                    if (this.validStats[stat] == 'ammoType') wepStats[this.validStats[stat]] = itemData['Ammo_' + calculate.s(this.validStats[stat])]['inGameName']
                    
                    else wepStats[this.validStats[stat]] = calculate.s(this.validStats[stat])
                }
                data.push(wepStats)
            }
            this.detailedData = data;
        },
        weaponImage(weapon: string, codeName: string) {
            let image = weapon
            if (codeName.includes("CUSTOM_WPN")) image = "Weapon_Icon"

            return `<img style="display: inline; width: 48px; max-height: 19px; margin: auto 0" src="map-images/item-images/${image.replace(' - Mk.II', '').replace(' - Prototype', '').replaceAll(' ', '_')}.png" /> <span> ${weapon} </span>`
        }
    },
    mounted() {
        let validStats = []
        for (let stat in this.headersStats) {
            if (parseInt(stat)  == 0) continue
            validStats.push(this.headersStats[stat]['value'])
        }
        this.validStats = validStats
        this.updateDetailedStats()
    },
    watch: {
        // listen to the global state utility to see if we should update the math from changes not made to the selected options
        updateGlobalStateUtility: {
            deep: true,
            handler() {
                this.updateDetailedStats();
                penetrationChart()
                falloffChart()
                ttkChart()
                stkChart()
            },
        },
        selectedWeapons: {
            deep: true,
            handler() {
                this.updateDetailedStats();
                penetrationChart()
                falloffChart()
                ttkChart()
                stkChart()
            },
        },
        selectedAttachments: {
            deep: true,
            handler() {
                this.updateDetailedStats();
                penetrationChart()
                falloffChart()
                ttkChart()
                stkChart()
            },
        },
        selectedTarget: {
            deep: true,
            handler() {
                this.updateDetailedStats();
                penetrationChart()
                ttkChart()
            },
        },
        selectedArmor: {
            deep: true,
            handler() {
                this.updateDetailedStats();
                penetrationChart()
                ttkChart()
            },
        },
        selectedHSValue: {
            deep: true,
            handler() {
                this.updateDetailedStats();
                stkChart()
            },
        },
        selectedAccuracy: {
            deep: true,
            handler() {
                this.updateDetailedStats()
                stkChart()
            }
        },
        selectedDistance: {
            deep: true,
            handler() {
                this.updateDetailedStats();
                falloffChart()
                ttkChart()
                stkChart()
            },
        },
        selectedWeakspotValue: {
            deep: true,
            handler() {
                this.updateDetailedStats();
                ttkChart()
            },
        },
    }
})
</script>
<style scoped>
h2 {
    border-bottom: 1px solid var(--border-color-base);
    width: 70%;
    margin: auto;
    margin-bottom: 1rem;
}
.wrapper {
    width: 80%;
    margin: auto;
    margin-bottom: var(--space-xl);
}

.wrapper-small {
    width: 50%;
}


.shots-container {
    display: grid;
    grid-template-columns: 1fr;
    gap: 2rem
}

@media  screen and (max-width: 1500px) {
    .wrapper-small {
        width: 80%;
        margin: auto;
        margin-bottom: var(--space-xl);
    }

    .shots-container {
        display: flex;
        flex-direction: column;
        grid-template-columns: 1fr;
    }
    
}

.customize-table {
  --easy-table-border: 1px solid var(--border-color-base);
  --easy-table-row-border: 1px solid var(--border-color-base);

  --easy-table-header-font-size: var(--space-md);
  --easy-table-header-height: var(--space-md);
  --easy-table-header-font-color: var(--color-base--emphasized);
  --easy-table-header-background-color: var(--color-surface-2);

  --easy-table-header-item-padding: 3px 1px;

  --easy-table-body-even-row-font-color: var(--color-base);
  --easy-table-body-even-row-background-color: var(--color-surface-4);

  --easy-table-body-row-font-color: var(--color-base);
  --easy-table-body-row-background-color: var(--color-surface-3);
  --easy-table-body-row-height: 10px;
  --easy-table-body-row-font-size: 1rem;

  --easy-table-body-row-hover-font-color: var(--color-surface-0);
  --easy-table-body-row-hover-background-color: var(--rarity-color-rare);

  --easy-table-body-item-padding: 3px 1px;

  --easy-table-footer-background-color: var(--color-surface-3);
  --easy-table-footer-font-color: var(--color-base);
  --easy-table-footer-font-size: 14px;
  --easy-table-footer-padding: 0px 10px;
  --easy-table-footer-height: 50px;

  --easy-table-rows-per-page-selector-width: 70px;
  --easy-table-rows-per-page-selector-option-padding: 10px;
  --easy-table-rows-per-page-selector-z-index: 1;


  --easy-table-scrollbar-track-color: var(--color-base);
  --easy-table-scrollbar-color: var(--color-base);
  --easy-table-scrollbar-thumb-color: 	#c1c1c1;;
  --easy-table-scrollbar-corner-color: pink;

  --easy-table-loading-mask-background-color: yellow;


}

div.name-wrapper {
    text-align: center;
    display: flex;
}

div.name-wrapper img {
    display: inline;
    width: 32px;
    height: 32px
}
div.options-wrapper {
    display: inline-flex;
    margin: auto;
    text-align: center;
}

h2 {
    text-align: center;
}

</style>