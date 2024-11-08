<template>
  <div>
    
    <div v-if="showNotification" class="notification">
      <p>{{ notificationMessage }}</p>
      <button v-if="deletedUser" @click="restoreUser" class="btn btn-link">Undo</button>
    </div>

    
    <button v-if="!sidebarOpen" class="hamburger-btn" type="button" @click="toggleSidebar">
      <span class="hamburger-line"></span>
      <span class="hamburger-line"></span>
      <span class="hamburger-line"></span>
    </button>

    <div :class="['sidebar', { 'active': sidebarOpen }]">
      <div class="sidebar-header">
        <h3>User Management</h3>
        <button class="close-btn" @click="toggleSidebar">&times;</button>
      </div>
      <ul class="list-group">
        <li @click="viewAddUser" class="list-group-item">Add User</li>
        <li @click="viewUsers" class="list-group-item">View Users</li>
        <li @click="viewTrash" class="list-group-item">Trash</li> 
      </ul>
    </div>

    
    <div class="container">
      <h1>User Management Application</h1>

      
      <form v-if="isAddingUser || selectedUser" @submit.prevent="!selectedUser ? addUser() : updateUser()">
        <div class="mb-4">
          <label for="name" class="label">Name</label>
          <input v-model="userForm.name" type="text" id="name" class="form-control" placeholder="Enter name" required />
        </div>
        <div class="mb-4">
          <label for="email" class="label">Email</label>
          <input v-model="userForm.email" type="email" id="email" class="form-control" placeholder="Enter email" required />
        </div>
        <div class="mb-4">
          <label for="phone" class="label">Phone</label>
          <input v-model="userForm.phone" type="tel" id="phone" class="form-control" placeholder="Enter phone number" required @input="validatePhone" />
          <small v-if="phoneError" class="text-danger">Phone number must contain only numbers.</small>
        </div>

        <div class="button-container">
          <button type="submit" class="btn btn-primary">{{ !selectedUser ? 'Add User' : 'Update User' }}</button>
        </div>

        <button v-if="selectedUser" type="button" class="btn btn-secondary mt-2" @click="cancelEdit">Cancel</button>
      </form>

      
      <div v-if="isViewingUsers" class="mt-5">
        <UserCard
          v-for="user in users"
          :key="user.id"
          :user="user"
          @edit-user="editUser"
          @delete-user="deleteUser"
        />
      </div>

      
      <div v-if="isViewingTrash" class="mt-5">
        <h2>Deleted Users</h2>
        <div v-if="deletedUsers.length === 0">
          <p>No deleted users.</p>
        </div>
        <div v-for="user in deletedUsers" :key="user.id" class="user-card">
          <div class="user-card-header">
            <h5>{{ user.name }}</h5>
            <button @click="restoreUser(user)" class="btn btn-success">Restore</button>
            <button @click="deletePermanently(user)" class="btn btn-danger ml-2">Delete Permanently</button>
          </div>
          <div class="user-card-body">
            <p><strong>Email:</strong> {{ user.email }}</p>
            <p><strong>Phone:</strong> {{ user.phone }}</p>
          </div>
        </div>
      </div>
    </div>

    <div class="image-background"></div>
  </div>
</template>

<script>
import UserCard from './components/UserCard.vue';
import { ref } from 'vue';

export default {
  components: { UserCard },
  setup() {
    const users = ref([]);
    const deletedUsers = ref([]); // Track deleted users
    const userForm = ref({ name: '', email: '', phone: '' });
    const selectedUser = ref(null);
    const phoneError = ref(false);
    const sidebarOpen = ref(false);
    const isAddingUser = ref(true);
    const isViewingUsers = ref(false);
    const isViewingTrash = ref(false); 
    const showNotification = ref(false);
    const notificationMessage = ref('');
    const deletedUser = ref(null);

    const toggleSidebar = () => {
      sidebarOpen.value = !sidebarOpen.value;
    };

    const viewAddUser = () => {
      isAddingUser.value = true;
      isViewingUsers.value = false;
      isViewingTrash.value = false;
      selectedUser.value = null;
      toggleSidebar();
    };

    const viewUsers = () => {
      isAddingUser.value = false;
      isViewingUsers.value = true;
      isViewingTrash.value = false;
      toggleSidebar();
    };

    const viewTrash = () => {
      isAddingUser.value = false;
      isViewingUsers.value = false;
      isViewingTrash.value = true;
      toggleSidebar();
    };

    const addUser = () => {
      if (!phoneError.value) {
        const newUser = { id: Date.now(), ...userForm.value };
        users.value.push(newUser);
        resetForm();
        showSuccessNotification('User added successfully');
      }
    };

    const updateUser = () => {
      const index = users.value.findIndex(user => user.id === selectedUser.value.id);
      if (index !== -1 && !phoneError.value) {
        users.value[index] = { ...selectedUser.value, ...userForm.value };
        resetForm();
        showSuccessNotification('User updated successfully');
      }
    };

    const deleteUser = (id) => {
      const userToDelete = users.value.find(user => user.id === id);
      const confirmed = window.confirm("Are you sure you want to delete this user?");
      if (confirmed) {
        deletedUsers.value.push(userToDelete);  
        users.value = users.value.filter(user => user.id !== id);
        showSuccessNotification('User deleted successfully');
      }
    };

    const restoreUser = (user) => {
      users.value.push(user);  
      deletedUsers.value = deletedUsers.value.filter(u => u.id !== user.id);
      showSuccessNotification('User restored successfully');
    };

    const deletePermanently = (user) => {
      const confirmed = window.confirm("Are you sure you want to permanently delete this user?");
      if (confirmed) {
        deletedUsers.value = deletedUsers.value.filter(u => u.id !== user.id);
        showSuccessNotification('User deleted permanently');
      }
    };

    const editUser = (user) => {
      selectedUser.value = user;
      userForm.value = { ...user }; 
      isAddingUser.value = false; 
    };

    const cancelEdit = () => {
      resetForm();
    };

    const resetForm = () => {
      userForm.value = { name: '', email: '', phone: '' };
      selectedUser.value = null;
      phoneError.value = false;
      isAddingUser.value = true; 
    };

    const validatePhone = () => {
      const phoneRegex = /^[0-9]*$/;
      phoneError.value = !phoneRegex.test(userForm.value.phone);
    };

    const showSuccessNotification = (message) => {
      notificationMessage.value = message;
      showNotification.value = true;
      setTimeout(() => {
        showNotification.value = false;
      }, 2000);
    };

    return {
      users,
      deletedUsers,
      userForm,
      selectedUser,
      phoneError,
      sidebarOpen,
      isAddingUser,
      isViewingUsers,
      isViewingTrash,
      showNotification,
      notificationMessage,
      deletedUser,  
      addUser,
      updateUser,
      deleteUser,
      restoreUser, 
      editUser,
      cancelEdit,
      validatePhone,
      toggleSidebar,
      viewAddUser,
      viewUsers,
      viewTrash,
      deletePermanently
    };
  }
};
</script>

<style scoped>

@import url('https://fonts.googleapis.com/css2?family=Lobster&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Arial:wght@400;600&display=swap');


.container {
  position: relative;
  z-index: 1;
  width: 100%;
  max-width: 600px; 
  margin: 40px auto 0; 
  background-color: rgba(255, 255, 255, 0.9); 
  padding: 40px 30px; 
  border-radius: 8px; 
  box-shadow: 0px 4px 12px rgba(0, 0, 0, 0.1); 
}


h1 {
  font-family: 'Lobster', cursive;
  color: #333;
  text-align: center;
  font-size: 2.5em;
  margin-bottom: 20px;
}
.button-container {
  display: flex;
  justify-content: center; 
  align-items: center;     
  width: 100%;             
  margin-top: 20px;        
}

h2 {
  font-family: 'Arial', sans-serif;
  font-weight: 600;
  font-size: 1.8em;
  color: #333;
  text-align: center;
  margin-top: 40px;
}


input[type="text"],
input[type="email"],
input[type="tel"],
textarea {
  font-family: 'Arial', sans-serif;
  font-weight: 600;
  border: 1px solid #ccc;
  padding: 10px;
  width: 100%;
  margin: 10px 0;
  border-radius: 6px;
}

button {
  padding: 10px 15px;
  font-weight: 600;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #5c6bc0;
}

.btn-primary {
  background-color: #607d8b;
  color: white;
}

.btn-secondary {
  background-color: #9e9e9e;
  color: white;
}

.btn-success {
  background-color: #388e3c;
  color: white;
}
.close-btn {
  background: none;
  border: none;
  color: white;
  font-size: 24px;
  cursor: pointer;
  position: absolute;
  top: 20px;  
  right: 20px;  
  z-index: 10; 
}


.btn-danger {
  background-color: #d32f2f;
  color: white;
}

.notification {
  position: absolute;
  top: 20px; 
  left: 50%;
  transform: translateX(-50%); 
  background-color: #28a745;
  color: white;
  padding: 10px;
  border-radius: 5px;
  font-family: 'Arial', sans-serif;
  font-size: 16px; 
  display: flex;
  justify-content: space-between;
  align-items: center;
  z-index: 9999;
}

.notification button {
  background: none;
  border: none;
  color: white;
  cursor: pointer;
  font-size: 14px; 
}


.user-card {
  background-color: #f1f1f1;
  padding: 20px;
  border-radius: 8px;
  margin-bottom: 20px;
}

.user-card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.user-card-body {
  margin-top: 10px;
}

.hamburger-btn {
  position: absolute;
  top: 20px;
  left: 20px;
  background: none;
  border: none;
  cursor: pointer;
}

.hamburger-btn span {
  display: block;
  width: 30px;
  height: 4px;
  background-color: #333;
  margin: 6px 0;
  transition: 0.4s;
}

.sidebar {
  position: fixed;
  top: 0;
  left: -250px;
  width: 250px;
  height: 100%;
  background-color: #333;
  color: white;
  padding-top: 60px;
  transition: left 0.3s;
}

.sidebar.active {
  left: 0;
}

.sidebar-header {
  padding: 10px;
  text-align: center;
}

.sidebar ul {
  list-style: none;
  padding: 0;
}

.sidebar ul li {
  padding: 15px;
  text-align: center;
}

.sidebar ul li:hover {
  background-color: #444;
}
.image-background {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background-image: url('@/assets/photo.jpeg');
  background-size: cover;
  filter: blur(5px);
  z-index: -1;
}

label[for="name"], label[for="email"], label[for="phone"] {
  font-weight: bold; 
  font-family: 'Arial', sans-serif;
  font-size: 16px;
}

</style>
