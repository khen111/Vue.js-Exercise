<template>
  <div class="user-card">
    <div class="user-card-header">
      <h5>{{ user.name }}</h5>
      <div class="user-card-actions">
        <button @click="editUser(user)" class="btn btn-secondary">Edit</button>
        <button @click="deleteUser(user.id)" class="btn btn-danger">Delete</button>
      </div>
    </div>
    <div class="user-card-body">
      <p><strong>Email:</strong> {{ user.email }}</p>
      <p><strong>Phone:</strong> {{ user.phone }}</p>
    </div>
  </div>
</template>

<script>
export default {
  props: ['user'],
  methods: {
    editUser(user) {
      this.$emit('edit-user', user);
    },
    deleteUser(id) {
      this.$emit('delete-user', id);
    }
  }
};
</script>

<style scoped>
.user-card {
  background-color: #f8f9fa;
  border-radius: 8px;
  padding: 20px;
  margin-bottom: 20px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.user-card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
}

.user-card-actions {
  display: flex;
  gap: 10px;
}

.user-card-body p {
  font-size: 14px;
  margin: 5px 0;
}

.btn {
  padding: 8px 12px;
  font-size: 14px;
  border-radius: 5px;
}

.btn-secondary {
  background-color: #ff7f50;
  color: white;
}

.btn-danger {
  background-color: #d9534f;
  color: white;
}
</style>
