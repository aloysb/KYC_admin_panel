<template>
  <div>
  <h1>login Page</h1>
  <Login />
</div>
</template>

<script>
// @ is an alias to /src
import Login from '@/components/Login';

export default {
  name: 'Home',
  components: {
    Login
  }
}
</script>
