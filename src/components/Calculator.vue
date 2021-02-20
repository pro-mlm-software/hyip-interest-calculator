<template>
  <div class="my-4">
    <h1 class="my-6 text-2xl font-semibold">LaraHYIP Interest Calculator</h1>
    <div class="container mx-auto flex justify-center">
      <div class="w-5/6">
        <div class="bg-white border p-4 rounded">
          <div class="flex bg-gray-100 p-4 border border-blue-500 rounded">
            <div class="w-1/2 mr-6">
              <label class="flex justify-start font-semibold mb-2"
                >Select Plan</label
              >
              <select
                id="plan"
                v-model="selected"
                class="flex justify-start w-1/2 border border-gray-200 p-2 rounded"
                v-on:change="onChange"
              >
                <option v-for="plan in plans" :key="plan.name" :value="plan.id">
                  {{ plan.name }}
                </option>
              </select>
            </div>
            <div class="w-1/2">
              <label class="flex justify-end font-semibold"
                >Invest Amount</label
              >
              <input name="amount" type="hidden" v-model="this.amount" />

              <div class="w-full flex justify-end">
                <span
                  class="flex items-end"
                  style="font-size:120%; color:#282828; font-weight:600"
                >
                  {{ this.amount }}
                </span>
                <span class="mr-1">(USD)</span>
                <input
                  id="rangeslider"
                  name="amount"
                  type="range"
                  :min="1"
                  :max="this.plan.max_amount"
                  v-model="amount"
                  v-on:input="onInput"
                  class="w-full rangeslider "
                />
              </div>
            </div>
          </div>
          <div class="my-6">
            <h3 class="text-xl font-semibold text-left my-4 text-blue-500">
              Plan Details
            </h3>

            <div class="flex bg-gray-100 p-4 border border-blue-500 rounded">
              <div class="w-1/6">
                <p class="text-sm text-gray-600 text-left mb-2">Plan Type</p>
                <p class="text-sm font-semibold text-left">
                  {{ this.plan.plantype.plantype }}
                </p>
              </div>
              <div class="w-1/6">
                <p class="text-sm text-gray-600 text-left mb-2">Duration</p>
                <p class="text-sm font-semibold text-left">
                  {{ this.plan.duration }} {{ this.plan.duration_key }}
                </p>
              </div>
              <div class="w-1/6">
                <p class="text-sm text-gray-600 text-left mb-2">
                  Principle Return
                </p>
                <p class="text-sm font-semibold text-left">
                  <span
                    v-if="parseInt(this.plan.principle_return) == 0"
                    class="success"
                  >
                    NO
                  </span>
                  <span v-else>YES</span>
                </p>
              </div>
              <div class="w-1/6">
                <p class="text-sm font-medium text-left mb-2">Compounting</p>
                <p class="text-sm font-semibold text-left">
                  <span
                    v-if="parseInt(this.plan.compounding) == 0"
                    class="success"
                  >
                    NO
                  </span>
                  <span v-else>YES</span>
                </p>
              </div>
              <div class="w-1/6">
                <p class="text-sm text-left mb-2">Amount (USD)</p>
                <p class="text-sm font-semibold text-left">
                  {{ this.plan.amount }}
                </p>
              </div>
              <div class="w-1/6">
                <p class="text-sm text-left mb-2">Interest Rate (%)</p>
                <p class="text-sm font-semibold text-left">
                  {{ this.plan.interest_rate }}
                </p>
              </div>
            </div>
          </div>
        </div>
        <div class="bg-white border p-4 rounded my-4">
          <div v-if="selected !== null" class="my-2">
            <h3 class="text-xl font-semibold text-left my-4 text-blue-500">
              Interest Details
            </h3>
          </div>
          <table
            class="w-full table table-vue shadow rounded"
            v-if="parseInt(this.plan.compounding) == 0"
          >
            <thead class="bg-gray-100">
              <th class="text-sm p-4 text-left" width="33.33%">
                Interest Amount(per {{ this.durationText }} ) USD
              </th>
              <th class="text-sm p-4 text-left" width="33.33%">
                Total Interest (USD)
              </th>
              <th class="text-sm p-4 text-left" width="33.33%">
                Total Return (USD)
              </th>
            </thead>
            <tbody class="">
              <tr class="border-b border-gray-200">
                <td class="px-4 py-2 text-left">
                  <span style="font-size:120%; color:#282828">{{
                    termInterest
                  }}</span>
                </td>
                <td class="px-4 py-2 text-left">
                  <span style="font-size:120%; color:#282828"
                    >{{ total }} (USD)</span
                  >
                </td>
                <td class="px-4 py-2">
                  <div class="flex flex-col">
                    <div class="text-xl text-left">
                      <span
                        class="text-left"
                        v-if="parseInt(this.plan.principle_return) == 0"
                      >
                        {{ total }} (USD)
                      </span>
                      <span class="text-left" v-else>{{ totalreturn }}</span>
                    </div>
                    <div class="text-sm">
                      <div
                        class="text-left"
                        v-if="parseInt(this.plan.principle_return) == 0"
                      >
                        {{ roinoreturn }} %
                      </div>
                      <div class="text-left" v-else>{{ roi }} %</div>
                    </div>
                  </div>
                </td>
              </tr>
            </tbody>
          </table>
          <table
            class="w-full table table-vue shadow rounded"
            v-if="parseInt(this.plan.compounding) == 1"
          >
            <thead class="bg-gray-100">
              <th class="text-sm text-left p-4" width="20%">Compound %</th>
              <th class="text-sm text-left p-4" width="25%">
                Interest Amount(per {{ this.durationText }} ) USD
              </th>
              <th class="text-sm text-left p-4">Total Interest (USD)</th>
              <th class="text-sm text-left p-4">Total Return (USD)</th>
            </thead>
            <tbody>
              <tr
                class="border-b border-gray-200"
                v-for="(item, index) in this.persentage"
                v-bind:key="index"
              >
                <td class="text-sm text-left px-4 py-2">
                  <span style="font-size:120%; color:#282828">{{ item }}</span>
                </td>
                <td class="text-sm text-left px-4 py-2">
                  <span style="font-size:120%; color:#282828">{{
                    terminterest(item)
                  }}</span>
                </td>

                <td class="text-sm text-left px-4 py-2">
                  <span style="font-size:120%; color:#282828" v-if="item == 0">
                    {{ total }}</span
                  >
                  <span style="font-size:120%; color:#282828" v-else>{{
                    compoundinterestsval(item)
                  }}</span>
                </td>
                <td class="text-sm text-left px-4 py-2">
                  <div class="flex flex-col">
                    <div>
                      <span v-if="item == 0">
                        <span
                          v-if="parseInt(plan.principle_return) == 0"
                          style="font-size:120%; color:#282828"
                        >
                          {{ total }}
                        </span>
                        <span v-else style="font-size:120%; color:#282828">{{
                          totalreturn
                        }}</span>
                      </span>
                      <span v-else>
                        <span
                          v-if="parseInt(plan.principle_return) == 0"
                          style="font-size:120%; color:#282828"
                        >
                          {{ compoundinterestsval(item) }}
                        </span>
                        <span v-else style="font-size:120%; color:#282828">
                          {{
                            (
                              parseFloat(amount) +
                              parseFloat(compoundinterestsval(item))
                            ).toFixed(2)
                          }}</span
                        >
                      </span>
                    </div>
                    <div class="text-sm">
                      <span v-if="parseInt(plan.principle_return) == 0">
                        {{
                          (
                            (compoundinterestsval(item) / parseFloat(amount)) *
                            100
                          ).toFixed(2)
                        }}
                        %
                      </span>
                      <span v-else>
                        {{
                          ((parseFloat(amount) +
                            parseFloat(compoundinterestsval(item))) /
                            parseFloat(amount)) *
                            100
                        }}
                        %
                      </span>
                    </div>
                  </div>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
    <div class="container mx-auto py-4">
      <div class="w-3/4 mx-auto">
        <p>
          Powered By
          <a href="https://phphyipmanagerscript.com">HYIP Manager Script</a>
        </p>
        <p class="text-sm my-3">
          PHP HYIP Manager Script is the most advanced and robust HYIP Software
          in Market Today. It supports Hourly Interest, Daily Interest, Daily
          Interest on Weekdays Only, Weekly Interest, Monthly Interest and
          Yearly Interest. If you are looking to build your own HYIP Business,
          buy the
          <a href="https://phphyipmanagerscript.com">Best HYIP Software.</a>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import moment from "moment";
import containedPeriodicValues from "contained-periodic-values";
import planjson from "../assets/json/plans.json";
export default {
  data() {
    return {
      plans: [],
      trans: [],
      plan: {
        id: "",
        name: "",
        min_amount: "",
        max_amount: "",
        interest_rate: "",
        duration_key: "",
        plantype_id: "",
        plantype: ""
      },
      persentage: [0, 25, 50, 75, 100],
      amount: "",
      selected: 0,
      total: "",
      totalreturn: "",
      roi: "",
      time: ""
    };
  },
  created() {
    this.plans = planjson;
    this.plan = this.plans[this.selected];
    this.total = (
      this.amount *
      (this.plan.interest_rate / 100) *
      this.duartionCyle
    ).toFixed(2);
    this.totalreturn = (
      parseFloat(this.amount) + parseFloat(this.total)
    ).toFixed(2);

    this.roinoreturn = (
      (parseFloat(this.total) / parseFloat(this.amount)) *
      100
    ).toFixed(2);
    this.roi = (
      (parseFloat(this.totalreturn) / parseFloat(this.amount)) *
      100
    ).toFixed(2);
  },
  computed: {
    myRoute: function() {
      console.log($router);
      return this.$router;
    },
    duration: function() {
      return this.plan.duration;
    },
    weekdays: function() {
      const startDay = moment().format("d");
      const totalDays = this.plan.duration;
      const endDays = parseInt(startDay) + parseInt(totalDays);

      const containedSundays = containedPeriodicValues(startDay, endDays, 0, 7);
      const containedSaturdays = containedPeriodicValues(
        startDay,
        endDays,
        6,
        7
      );

      return totalDays - (containedSaturdays + containedSundays);
    },
    durationText: function() {
      if (this.plan.plantype_id == 3) {
        return "Hour";
      } else if (this.plan.plantype_id == 2) {
        return "Daily (Mon - Fri)";
      } else {
        return this.plan.duration_key.substring(
          0,
          this.plan.duration_key.length - 1
        );
      }
    },
    duartionCyle: function() {
      if (this.plan.plantype_id == 3) {
        return this.plan.duration * 24;
      } else {
        return this.plan.duration;
      }
    },
    termInterest: function() {
      return parseFloat(this.amount * (this.plan.interest_rate / 100)).toFixed(
        2
      );
    }
  },
  mounted() {
    this.plans = planjson;
    this.plan = this.plans[this.selected];
    this.amount = this.plan.amount;
    this.total = (
      this.plan.amount *
      (this.plan.interest_rate / 100) *
      this.duartionCyle
    ).toFixed(2);
    this.totalreturn = (
      parseFloat(this.amount) + parseFloat(this.total)
    ).toFixed(2);

    this.roinoreturn = (
      (parseFloat(this.total) / parseFloat(this.plan.amount)) *
      100
    ).toFixed(2);
    this.roi = (
      (parseFloat(this.totalreturn) / parseFloat(this.amount)) *
      100
    ).toFixed(2);
  },
  methods: {
    onChange: function() {
      this.plan = this.plans[this.selected];
      this.amount = this.plan.amount;
      this.total = (
        this.amount *
        (this.plan.interest_rate / 100) *
        this.duartionCyle
      ).toFixed(2);
      this.totalreturn = (
        parseFloat(this.amount) + parseFloat(this.total)
      ).toFixed(2);

      this.roinoreturn = (
        (parseFloat(this.total) / parseFloat(this.amount)) *
        100
      ).toFixed(2);
      this.roi = (
        (parseFloat(this.totalreturn) / parseFloat(this.amount)) *
        100
      ).toFixed(2);
    },
    onInput: function() {
      this.total = (
        this.amount *
        (this.plan.interest_rate / 100) *
        this.duartionCyle
      ).toFixed(2);
      this.totalreturn = (
        parseFloat(this.amount) + parseFloat(this.total)
      ).toFixed(2);
      this.roinoreturn = (
        (parseFloat(this.total) / parseFloat(this.amount)) *
        100
      ).toFixed(2);
      this.roi = (
        (parseFloat(this.totalreturn) / parseFloat(this.amount)) *
        100
      ).toFixed(2);
    },
    terminterest(per) {
      if (per == 0) {
        return this.termInterest;
      } else if (per == 100) {
        return 0;
      } else {
        return parseFloat(this.termInterest * (per / 100)).toFixed(2);
      }
    },
    compoundinterestsval(per) {
      var simper = 0;
      if (per == 25) {
        simper = 75;
      } else if (per == 50) {
        simper = 50;
      } else if (per == 75) {
        simper = 25;
      }

      const interest = this.plan.interest_rate / 100;
      const duration = this.plan.duration;

      if (this.plan.duration_key == "years") {
        this.time = 1;
      } else if (this.plan.duration_key == "months") {
        this.time = this.plan.duration / 12;
      } else if (this.plan.duration_key == "weeks") {
        this.time = this.plan.duration / 52;
      } else if (this.plan.duration_key == "hours") {
        this.time = this.plan.duration / (365 * 24);
      } else if (this.plan.duration_key == "days") {
        if (this.plan.plantype_id == 2) {
          this.time = this.weekdays / 365;

          const duration = this.weekdays;
        } else {
          this.time = this.plan.duration / 365;
        }
      } else {
        this.time = 0;
      }

      if (this.plan.plantype_id == 8) {
        if (per == "25" || per == "50" || per == "75") {
          const inter = (this.plan.interest_rate * parseFloat(simper)) / 100;

          const inter50 = (interest * parseFloat(per)) / 100;

          return (
            parseFloat(this.amount * (inter / 100)) * duration +
            (parseFloat(this.amount * Math.pow(1 + inter50, duration)) -
              parseFloat(this.amount))
          ).toFixed(2);
        } else {
          return (
            parseFloat(this.amount * Math.pow(1 + interest, duration)) -
            parseFloat(this.amount)
          ).toFixed(2);
        }
      } else if (this.plan.plantype_id == 9) {
        if (per == "25" || per == "50" || per == "75") {
          const inter = (this.plan.interest_rate * parseFloat(simper)) / 100;

          const inter50 = (interest * parseFloat(per)) / 100;
          const inter25 = (interest * parseFloat(simper)) / 100;

          return (
            parseFloat(this.amount * (inter / 100)) * duration +
            (parseFloat(this.amount * Math.pow(1 + inter50, 12 * this.time)) -
              parseFloat(this.amount))
          ).toFixed(2);
        } else {
          return (
            parseFloat(this.amount * Math.pow(1 + interest, 12 * this.time)) -
            parseFloat(this.amount)
          ).toFixed(2);
        }
      } else if (this.plan.plantype_id == 7) {
        if (per == "25" || per == "50" || per == "75") {
          const inter = (this.plan.interest_rate * parseFloat(simper)) / 100;

          const inter50 = (interest * parseFloat(per)) / 100;
          const inter25 = (interest * parseFloat(simper)) / 100;

          return (
            parseFloat(this.amount * (inter / 100)) * duration +
            (parseFloat(this.amount * Math.pow(1 + inter50, 4 * this.time)) -
              parseFloat(this.amount))
          ).toFixed(2);
        } else {
          return (
            parseFloat(this.amount * Math.pow(1 + interest, 4 * this.time)) -
            parseFloat(this.amount)
          ).toFixed(2);
        }
      } else if (this.plan.plantype_id == 6) {
        if (per == "25" || per == "50" || per == "75") {
          const inter = (this.plan.interest_rate * parseFloat(simper)) / 100;

          const inter50 = (interest * parseFloat(per)) / 100;
          const inter25 = (interest * parseFloat(simper)) / 100;

          return (
            parseFloat(this.amount * (inter / 100)) * duration +
            (parseFloat(this.amount * Math.pow(1 + inter50, 12 * this.time)) -
              parseFloat(this.amount))
          ).toFixed(2);
        } else {
          return (
            parseFloat(this.amount * Math.pow(1 + interest, 12 * this.time)) -
            parseFloat(this.amount)
          ).toFixed(2);
        }
      } else if (this.plan.plantype_id == 5) {
        if (per == "25" || per == "50" || per == "75") {
          const inter = (this.plan.interest_rate * parseFloat(simper)) / 100;

          const inter50 = (interest * parseFloat(per)) / 100;

          return (
            parseFloat(this.amount * (inter / 100)) * duration +
            (parseFloat(this.amount * Math.pow(1 + inter50, 52 * this.time)) -
              parseFloat(this.amount))
          ).toFixed(2);
        } else {
          return (
            parseFloat(this.amount * Math.pow(1 + interest, 52 * this.time)) -
            parseFloat(this.amount)
          ).toFixed(2);
        }
      } else if (this.plan.plantype_id == 4) {
        if (per == "25" || per == "50" || per == "75") {
          const inter = (this.plan.interest_rate * parseFloat(simper)) / 100;

          const inter50 = (interest * parseFloat(per)) / 100;

          return (
            parseFloat(this.amount * (inter / 100)) * duration +
            (parseFloat(
              this.amount * Math.pow(1 + inter50, 365 * 24 * this.time)
            ) -
              parseFloat(this.amount))
          ).toFixed(2);
        } else {
          return (
            parseFloat(
              this.amount * Math.pow(1 + interest, 365 * 24 * this.time)
            ) - parseFloat(this.amount)
          ).toFixed(2);
        }
      } else if (
        this.plan.plantype_id == 1 ||
        this.plan.plantype_id == 2 ||
        this.plan.plantype_id == 3
      ) {
        if (per == "25" || per == "50" || per == "75") {
          const inter = (this.plan.interest_rate * parseFloat(simper)) / 100;

          const inter50 = (interest * parseFloat(per)) / 100;

          const inter25 = (interest * parseFloat(simper)) / 100;

          return (
            parseFloat(this.amount * (inter / 100)) * duration +
            (parseFloat(this.amount * Math.pow(1 + inter50, 365 * this.time)) -
              parseFloat(this.amount))
          ).toFixed(2);
        } else {
          return (
            parseFloat(this.amount * Math.pow(1 + interest, 365 * this.time)) -
            parseFloat(this.amount)
          ).toFixed(2);
        }
      } else {
        return "-";
      }
    }
  }
};
</script>
<style scoped></style>
