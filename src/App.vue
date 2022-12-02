<template>
  <div id="app">
    <p>Profiles List</p>
    <div class="section">
      <div class="flex-row">
        <label class="label" for="filter">Find profile:</label>
        <input class="input" v-model="search">
      </div>
      <div class="buttons">
        <button @click="sortAsc">▲</button>
        <button @click="sortDesc">▼</button>
      </div>

      <ProfileCard
          v-for="(profile, index) in filterProfile"
          :key="index"
          @likeAction="likeAction(profile.id)"
          :profile="profile"
          class="profile"
      />

      <div class="icons-note">
        Icons made by
        <a href="https://www.flaticon.com/authors/freepik" title="Freepik">Freepik</a> from
        <a href="https://www.flaticon.com/" title="Flaticon">www.flaticon.com</a>
      </div>
    </div>

    <div class="section">
      <p class="header">Add new profile:</p>
      <div class="flex-row">
        <label class="label">Name:</label>
        <input class="input" v-model="addForm.name">
      </div>
      <div class="flex-row">
        <label class="label" for="filter">Email:</label>
        <input class="input" type="email" v-model="addForm.email">
      </div>
      <div class="flex-row">
        <label class="label">Specialisation:</label>
        <select v-model="addForm.specialisation" multiple class="select">
          <option v-for="(item,index) in specialisationsList" :value="item" :key="index">
            {{ item }}
          </option>
        </select>
      </div>
      <button @click="addProfile">Add</button>
    </div>
  </div>
</template>

<script>
import ProfileCard from "./components/ProfileCard";

export default {
  name: "App",

  components: {
    ProfileCard
  },

  data() {
    return {
      specialisationsList: [
        'Surgeon', 'Radiologist', 'Cardiologist', 'Psychiatrist', 'Dermatologist'
      ],
      search: '',
      filterProfile: [],
      addForm: {
        id: 0,
        name: "",
        email: "",
        specialisation: [],
        likes: 0,
        liked: false
      },
      profiles: [
        {
          id: 1,
          name: "Wojciech",
          email: "wojciech@poz.pl",
          specialisation: "Anaesthesiologist",
          likes: 34,
          liked: false
        },
        {
          id: 2,
          name: "Maria",
          email: "maria@poz.pl",
          specialisation: "Radiologist",
          likes: 28,
          liked: false
        },
        {
          id: 3,
          name: "Anna",
          email: "anna@poz.pl",
          specialisation: "Surgeon",
          likes: 53,
          liked: false
        }
      ]
    };
  },
  methods: {
    sortAsc() {
      this.profiles.sort(function (a, b) {
        return a.likes - b.likes;
      });
    },

    sortDesc() {
      this.profiles.sort(function (a, b) {
        return b.likes - a.likes;
      });
    },
    likeAction(personId) {
      const person = this.profiles.find((item) => {
        return item.id === personId
      })
      if (person.liked) {
        person.likes--
      } else {
        person.likes++
      }
      person.liked = !person.liked

    },
    addProfile() {
      if (this.addForm.name.length &&
          this.addForm.email.length &&
          this.validateEmail(this.addForm.email) &&
          this.validateName(this.addForm.name)
      ) {
        this.addForm.id = this.profiles.length
        this.addForm.specialisation = this.addForm.specialisation.length ? this.addForm.specialisation.join(', ') : this.addForm.specialisation
        this.profiles.push(JSON.parse(JSON.stringify(this.addForm)))
        Object.assign(this.addForm, {
          name: "",
          email: "",
          specialisation: [],
        })
      } else {
        alert('Enter Valid Name And Email')
      }
    },
    validateName(name) {
      return /^[a-zA-Z]+$/.test(name)
    },
    validateEmail(email) {
      return String(email)
          .toLowerCase()
          .match(
              /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
          );
    }
  },
  created() {
    this.filterProfile = this.profiles
  },
  watch: {
    search(val) {
      if (val && val.length) {
        this.filterProfile = this.profiles.filter((item) => {
          console.log(item.name)
          console.log(new RegExp(val, 'i'))
          console.log(new RegExp(val, 'i').test(item.name))
          return new RegExp(val, 'i').test(item.name)
        })
      } else {
        this.filterProfile = this.profiles
      }
    }
  }
};
</script>

<style>
#app {
  font-family: "Roboto", helvetica, arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  padding: 60px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  position: relative;

  background: linear-gradient(
      135deg,
      rgba(65, 184, 131, 0.9),
      rgba(52, 73, 94, 0.9)
  );

  font-size: 1.5em;
}

button {
  display: block;
  padding: 1em;
  width: 100%;
  background-color: #41B883;
  border: 1px solid;
  color: #fff;
  cursor: pointer;
  font-size: 0.75em;
  font-weight: 600;
  text-shadow: 0 1px 0 rgba(black, 0.2);
}

.content {
  display: flex;
}

.section {
  width: 100%;
  min-width: 300px;
  padding: 2em;
  margin-top: 30px;
  position: relative;
  background: rgba(0, 0, 0, 0.15);
}

@media screen and (min-width: 600px) {
  .section {
    width: 50vw;
    max-width: 15em;
  }
}

.header {
  color: #fff;
}

.section::before {
  content: "";
  position: absolute;
  top: -2px;
  left: 0;
  height: 2px;
  width: 100%;
  background: #35c3c1;
}

.flex-row {
  display: flex;
  margin-bottom: 1em;
}

.label {
  width: 80px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 12px;
  background: #f5f6f8;
}

.input {
  flex: 1;
  padding: 1em;
  border: 0;
  color: #8f8f8f;
  font-size: 1rem;
}

.select {
  flex: 1;
  height: 50px;
  padding: 10px;
  border: 0;
  color: #8f8f8f;
  font-size: 1rem;
  border-radius: 0 !important;
}

.buttons {
  display: flex;
  box-shadow: 0 10px 10px rgba(0, 0, 0, 0.25), 0 5px 5px rgba(0, 0, 0, 0.22);
  margin-top: 30px;
}

.profile {
  margin-top: 20px;
}

.icons-note {
  margin-top: 30px;
  font-size: 10px;
}
</style>
