<template>
  <div class="dashboard">
    <h2>Dashboard</h2>
    

    <div class="dashboard-sections">
      <div class="card" @click="$emit('navigate', 'addUser')">
        <div class="card-body">
          <h5 class="card-title">Add User</h5>
          <p class="card-text">Click to add a new user.</p>
        </div>
      </div>
      
      <div class="card" @click="$emit('navigate', 'userList')">
        <div class="card-body">
          <h5 class="card-title">User List</h5>
          <p class="card-text">Click to view the list of users.</p>
        </div>
      </div>
      
      <div class="card" @click="$emit('navigate', 'dashboard')">
        <div class="card-body">
          <h5 class="card-title">Dashboard</h5>
          <p class="card-text">Return to the main dashboard.</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "UserDashboard",
};
</script>

<style scoped>
.dashboard {
  padding: 20px;
  background-color: #f8f9fa;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  text-align: center;
}

.dashboard-sections {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
}

.card {
  cursor: pointer;
  transition: transform 0.2s ease-in-out;
}

.card:hover {
  transform: scale(1.05);
}

.card-body {
  padding: 20px;
  background-color: #ffffff;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.card-title {
  font-size: 1.25rem;
  margin-bottom: 10px;
}

.card-text {
  font-size: 1rem;
}
</style>
