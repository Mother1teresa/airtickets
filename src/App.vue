<script setup>
import AppHeader from "./components/AppHeader.vue";
</script>


<script>
export default {
  data() {
    
    return {
      clicked: false,
      filters: {
      all: {
        id: "all",
        sectionText: "Все",
        status: true,
      },
      sections: [
        {
          id: "notTransfer",
          sectionText: "Без пересадок",
          status: false,
        },
        {
          id: "1Transfer",
          sectionText: "1 пересадка",
          status: false,
        },
        {
          id: "2Transfer",
          sectionText: "2 пересадки",
          status: false,
        },
        {
          id: "3Transfer",
          sectionText: "3 пересадки",
          status: false,
        },
        ],
     },
     

      sortParameter: 'price',

      tickets: [
        {
          id: 1,
          price: "18 400",
          imageUrl: "src/assets/s7.png",
          timeTitleOne: "mow - hkt",
          timeTextOne: "10:25 - 06:45",
          wayTitleOne: "в пути",    
          wayTextOneHours: "25",
          wayTextOneMun:"45",
          transfersTitleOne: "2 пересадки",
          transfersTextOne: "hkg, nbj",
          timeTitleTwo: "mow - hkt",
          timeTextTwo: "10:45 - 08:00",
          wayTitleTwo: "в пути",
          wayTextTwo: "21ч 15м",
          transfersTitleTwo: "2 пересадки",
          transfersTextTwo: "hkg, jnb",
        },
        {
          id: 2,
          price: "14 700",
          imageUrl: "src/assets/Rossiya-Airlines.jpg",
          timeTitleOne: "mow - hkt",
          timeTextOne: "10:25 - 10:10",
          wayTitleOne: "в пути",
          wayTextOneHours: "22",
          wayTextOneMun:"25",
          transfersTitleOne: "2 пересадки",
          transfersTextOne: "hkg, nbj",
          timeTitleTwo: "mow - hkt",
          timeTextTwo: "11:10 - 08:00",
          wayTitleTwo: "в пути",
          wayTextTwo: "20ч 50м",
          transfersTitleTwo: "3 пересадки",
          transfersTextTwo: "hkg, jnb, fds",
        },
        {
          id: 3,
          price: "27 400",
          imageUrl: "src/assets/transaero.png",
          timeTitleOne: "mow - hkt",
          timeTextOne: "11:45 - 21:00",
          wayTitleOne: "в пути",
          wayTextOneHours: "9",
          wayTextOneMun:"15",
          transfersTitleOne: "1 пересадки",
          transfersTextOne: "hkg",
          timeTitleTwo: "mow - hkt",
          timeTextTwo: "10:45 - 21:00",
          wayTitleTwo: "в пути",
          wayTextTwo: "11ч 15м",
          transfersTitleTwo: "1 пересадки",
          transfersTextTwo: "hkg",
        },
      ],
      
      
    };
  },
  watch: {
    'filters.all.status'(val) {
      if (val) {
        this.filters.sections.forEach(n => n.status = false);
      }
    },
    'filters.sections': {
      deep: true,
      handler(val) {
        if (val.every(n => n.status)) {
          this.filters.all.status = true;
        } else if (val.some(n => n.status)) {
          this.filters.all.status = false;
        }
      }
    },
  },
  computed: {
    sortedTickets() {
    let filteredTickets = this.tickets;

    if (!this.filters.all.status) {
      filteredTickets = filteredTickets.filter(ticket => {
        const transfersCount = ticket.transfersTitleOne.split(" ")[0];

        if (this.filters.sections.some(section => section.status && section.id === transfersCount + "Transfer")) {
          return true;
        }

        return false;
      });
    }

    if (this.sortParameter === 'price') {
      filteredTickets.sort((a, b) => {
        return (
          parseInt(a.price.replace(/\s/g, "")) -
          parseInt(b.price.replace(/\s/g, ""))
        );
      });
    } else if (this.sortParameter === 'time') {
      filteredTickets.sort((a, b) => {
        if (a.wayTextOneHours === b.wayTextOneHours) {
          return a.wayTextOneMun - b.wayTextOneMun;
        } else {
          return a.wayTextOneHours - b.wayTextOneHours;
        }
      });
    }

    return filteredTickets;
  },
  sectionsWithAll() {
      return [this.filters.all].concat(this.filters.sections);
    },
},
  
  
 

  methods: {
    sortTickets(parameter) {
      this.sortParameter = parameter;
    },
   
  },
};
</script>

<template>
  <AppHeader />

  <main>
     <div class="search-form">
        <div class="search-form__content box" >
          <div class="search-form__title">количество пересадок</div>
          <div class="search-form__input" >
            <input type="checkbox" class="custom-checkbox" :id="filters.all.id" v-model="filters.all.status">
            <label :for="filters.all.id">{{ filters.all.sectionText }}</label>
          </div>
          <div class="search-form__input" v-for="section in filters.sections" :key="section.id">
            <input type="checkbox" class="custom-checkbox" :id="section.id" v-model="section.status">
            <label :for="section.id">{{ section.sectionText }}</label>
          </div>
          </div>
      </div>
    <div class="union-content">
      <div class="switch">
        <button
          class="switch__button size-btn switch__button-cheap" v-bind:class="clicked ? 'white' : 'blue'" v-on:click="clicked = !clicked"
          @click="sortTickets('price')" >
          <!-- onclick="selectTab('billing')" :style="getStyle('billing')" -->
          Самый дешевый
        </button>
        <button
          class="switch__button size-btn switch__button-faster" v-bind:class="clicked ? 'blue' : 'white'" v-on:click="clicked = !clicked"
          @click="sortTickets('time')"  >
          <!-- onclick="selectTab('shipping')" :style="getStyle('shipping')" -->
          Самый быстрый
        </button>
      </div>
      <div class="cards">
        <div class="card box" v-for="ticket in sortedTickets" :key="ticket.wayTextOneHours + ticket.wayTextOneMun">
          <div class="card-airlines">
            <div class="card-price">{{ ticket.price }} P</div>
            <div class="card-Airoflot">
              <img :src="ticket.imageUrl" alt="Image" />
            </div>
          </div>
          <div class="card-tickets">
            <div class="card-tickets-time">
              <div class="card-tickets-time__title">
                {{ ticket.timeTitleOne }}
              </div>
              <div class="card-tickets-time__text">
                {{ ticket.timeTextOne }}
              </div>
            </div>
            <div class="card-way">
              <div class="card-way__title">{{ ticket.wayTitleOne }}</div>
              <div class="card-way__text">{{ ticket.wayTextOneHours }}ч {{ ticket.wayTextOneMun }}м</div>
            </div>
            <div class="card-transfers">
              <div class="card-transfers__title">
                {{ ticket.transfersTitleOne }}
              </div>
              <div class="card-transfers__text">
                {{ ticket.transfersTextOne }}
              </div>
            </div>
          </div>
          <div class="card-tickets">
            <div class="card-tickets-time">
              <div class="card-tickets-time__title">
                {{ ticket.timeTitleTwo }}
              </div>
              <div class="card-tickets-time__text">
                {{ ticket.timeTextTwo }}
              </div>
            </div>
            <div class="card-way">
              <div class="card-way__title">{{ ticket.wayTitleTwo }}</div>
              <div class="card-way__text">{{ ticket.wayTextTwo }}</div>
            </div>
            <div class="card-transfers">
              <div class="card-transfers__title">
                {{ ticket.transfersTitleTwo }}
              </div>
              <div class="card-transfers__text">
                {{ ticket.transfersTextTwo }}
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- <div  class="cards">
        
        <div class="card box" v-for="ticket in sortedTickets" :key="ticket.id">
          <div class="card-airlines">
            <div class="card-price">{{ ticket.price }} P</div>
            <div class="card-Airoflot">
              <img :src="ticket.imageUrl" :alt="Image" />
            </div>
          </div>
          <div class="card-tickets">
            <div class="card-tickets-time">
              <div class="card-tickets-time__title">{{ ticket.timeTitleOne }}</div>
              <div class="card-tickets-time__text">{{ ticket.timeTextOne }}</div>
            </div>
            <div class="card-way">
              <div class="card-way__title">{{ ticket.wayTitleOne }}</div>
              <div class="card-way__text">{{ ticket.wayTextOne }}</div>
            </div>
            <div class="card-transfers">
              <div class="card-transfers__title">{{ ticket.transfersTitleOne }}</div>
              <div class="card-transfers__text">{{ ticket.transfersTextOne }}</div>
            </div>
          </div>
          <div class="card-tickets">
            <div class="card-tickets-time">
              <div class="card-tickets-time__title">{{ ticket.timeTitleTwo }}</div>
              <div class="card-tickets-time__text">{{ ticket.timeTextTwo }}</div>
            </div>
            <div class="card-way">
              <div class="card-way__title">{{ ticket.wayTitleTwo }}</div>
              <div class="card-way__text">{{ ticket.wayTextTwo }}</div>
            </div>
            <div class="card-transfers">
              <div class="card-transfers__title">{{ ticket.transfersTitleTwo }}</div>
              <div class="card-transfers__text">{{ ticket.transfersTextTwo }}</div>
            </div>
          </div>
        </div>
      </div> -->
    </div>
  </main>
</template>

<style scoped>
main {
  display: flex;
  grid-gap: 1.3rem;
  margin-top: 4rem;
  height: 100%;
}
.search-form__content {
  background: #fff;
  border-radius: 0.425rem;
  padding: 1rem 0;
  width: 250px;
}
.box {
  box-shadow: 0px 3px 5px 0px rgba(0, 0, 0, 0.142);
}
.search-form__title {
  font-size: 0.7rem;
  color: #393939e8;
  text-transform: uppercase;
  padding-left: 1.3rem;
  margin-bottom: 0.7rem;
}
.search-form__input {
  color: #0c0c0cbb;
  font-size: 0.8rem;
  padding: 10px;
  /* height: 15px; */
  padding-left: 1.3rem;
}
.search-form__input:hover {
  cursor: pointer;
  background: #bce1ff43;
}

.custom-checkbox {
  position: absolute;
  z-index: -1;
  opacity: 0;
  margin-right: 2rem;
} 
.custom-checkbox + label {
  display: inline-flex;
  align-items: center;
  user-select: none;
}
.custom-checkbox + label::before {
  content: "";
  display: inline-block;
  width: 1rem;
  height: 1rem;
  flex-shrink: 0;
  flex-grow: 0;
  border: 1px solid #447cbda5;
  border-radius: 0.15em;
  margin-right: 0.5em;
  background-repeat: no-repeat;
  background-position: center center;
  background-size: 50% 50%;
}
.custom-checkbox:checked + label::before {
  border-color: #0b76ef;
  background-color: #0b76ef;
  background-image: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 8 8'%3e%3cpath fill='%23fff' d='M6.564.75l-3.59 3.612-1.538-1.55L0 4.26 2.974 7.25 8 2.193z'/%3e%3c/svg%3e");
}


.switch {
  width: 100%;
  height: 3.4rem;
  display: flex;
  flex-wrap: wrap;
}
.switch__button {
  padding: 1rem 2rem;
  text-align: center;
  font-size: 0.78rem;
  color: #2a2a2a;
  text-transform: uppercase;
  background: #fff;
  border: 0.01rem solid #35353528;
}
.size-btn {
  width: 50%;
}
.blue{
  background: #0b76ef;
  color: #fff;
}
.white{
  background: white;
  color: #000;
}
.white:hover{
  background: #2a7fe1cd;
  color: #fff;
}
.switch__button-cheap {
  border-radius: 0.425rem 0 0 0.425rem;
}
.switch__button-faster {
  border-radius: 0 0.425rem 0.425rem 0;
}
/* .switch__button-faster:hover{
  background: #126dd5b0;
  color: #fff;
} */
.union-content {
  display: grid;
  width: 100%;
  grid-gap: 1rem;
}

.card {
  background: #fff;
  border-radius: 0.425rem;
  padding: 1.6rem;
  max-width: 100%;
}
.cards {
  display: grid;
  grid-gap: 1rem;
}
.card-Airoflot img {
  width: 6.3rem;
  height: 2rem;
}
.card-price {
  font-size: 1.3rem;
  color: #0b76ef;
}
.card-airlines {
  display: flex;
  justify-content: space-between;
  /* padding: 1.2rem 2rem 0 2.7rem; */
}
.card-tickets {
  display: flex;
  justify-content: space-between;
  margin-top: 1rem;
  width: 95%;
}
.card-tickets .card-transfers {
  width: 20%;
}
.card-tickets-time,
.card-way,
.card-transfers {
  display: grid;
  grid-gap: 0.3rem;
}
.card-tickets-time__title,
.card-way__title,
.card-transfers__title {
  color: #a0a0a0de;
  font-size: 0.7rem;
  text-transform: uppercase;
}
.card-transfers__text {
  text-transform: uppercase;
}
.card-tickets-time__text,
.card-way__text,
.card-transfers__text {
  font-size: 0.8rem;
}
</style>
