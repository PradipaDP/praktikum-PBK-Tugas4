<template>
  <div id="app">
    <!-- Header -->
    <header>
      <nav>
        <ul>
          <li @click="selectedTab = 'Todos'" :class="{ 'active': selectedTab === 'Todos' }">Todos</li>
          <li @click="selectedTab = 'Post'" :class="{ 'active': selectedTab === 'Post' }">Post</li>
        </ul>
      </nav>
    </header>

    <!-- Main Content -->
    <div class="container">
      <!-- Todos Component -->
      <div v-if="selectedTab === 'Todos'">
        <AddActivity @add="addActivity" />
        <ActivityList :activities="activities" @cancel="cancelActivity" @complete="completeActivity" />
      </div>

      <!-- Post Component -->
      <div v-else-if="selectedTab === 'Post'">
        <!-- Select User -->
        <select v-model="selectedUser" class="user-dropdown">
          <option v-for="user in users" :key="user.id" :value="user.id">{{ user.name }}</option>
        </select>
        <!-- Display User's Todos -->
        <div v-if="selectedUser" class="user-todos">
          <h2>{{ selectedUserName }}'s Todos</h2>
          <ul>
            <li v-for="todo in userTodos" :key="todo.id">
              <p>{{ todo.title }}</p>
              <span v-if="todo.completed" style="color: green;">Completed</span>
              <span v-else style="color: red;">Not Completed</span>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';
import ActivityList from './components/ActivityList.vue';
import AddActivity from './components/AddActivity.vue';

export default {
  components: {
    ActivityList,
    AddActivity
  },
  setup() {
    const activities = ref([]);
    const selectedTab = ref('Todos');
    const selectedUser = ref(null);
    const selectedUserName = ref('');
    const users = ref([]);
    const userTodos = ref([]);

    // Load activities from local storage when the app starts
    onMounted(() => {
      if (localStorage.getItem('activities')) {
        activities.value = JSON.parse(localStorage.getItem('activities'));
      }
      fetchUsers();
    });

    const addActivity = (name) => {
      const newActivity = { id: Date.now(), name, completed: false };
      activities.value.unshift(newActivity);
      console.log(`Added activity: ${name}`);
      saveActivities();
    };

    const cancelActivity = (id) => {
      const index = activities.value.findIndex(activity => activity.id === id);
      if (index > -1) {
        activities.value.splice(index, 1);
        saveActivities();
      }
    };

    const completeActivity = (id) => {
      const index = activities.value.findIndex(activity => activity.id === id);
      if (index > -1) {
        activities.value[index].completed = !activities.value[index].completed;
        saveActivities();
      }
    };

    const saveActivities = () => {
      localStorage.setItem('activities', JSON.stringify(activities.value));
    };

    const fetchUsers = () => {
      fetch('https://my-json-server.typicode.com/PradipaDP/resourcedata/users')
        .then(response => response.json())
        .then(data => users.value = data)
        .catch(error => console.error('Error fetching users:', error));
    };

    const fetchUserTodos = () => {
      if (selectedUser.value) {
        const user = users.value.find(user => user.id === selectedUser.value);
        if (user) {
          userTodos.value = user.todos;
          selectedUserName.value = user.name;
        }
      }
    };

    return {
      activities,
      addActivity,
      cancelActivity,
      completeActivity,
      selectedTab,
      selectedUser,
      selectedUserName,
      users,
      userTodos,
      fetchUserTodos
    };
  },
  watch: {
    selectedUser() {
      this.fetchUserTodos();
    }
  }
}
</script>


<style scoped>
#app {
  width: 1700px;
  max-width: 1000px;
  margin: 40px auto;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  background: #98c1d9;
}

header {
  background-color: #333;
  padding: 10px 10px 35px 10px;
}

nav {
  float: left;
}

nav ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

nav ul li {
  display: inline-block;
  margin-right: 20px;
  cursor: pointer;
}

nav ul li.active {
  font-weight: bold;
  color: #fff;
}

/* Main Content */
.container {
  width: 1700px;
  max-width: 975px;
  margin: 20px auto;
  margin-left: 10px;
  text-align: left; /* Align child elements to the left */
}

/* Adjust the user dropdown */
.user-dropdown {
  width: calc(20% - 24px); /* Subtract padding and border width */
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-bottom: 10px; /* Add some bottom margin for spacing */
}

/* User's Todos */
.user-todos {
  margin-top: 20px;
}

.user-todos h2 {
  margin-bottom: 10px;
}
</style>
