<script setup>
import Logo from '@/assets/images/logo.svg'
import { ref } from 'vue'

const isMobileMenuOpen = ref(false)
</script>

<template>
  <header class="header">
    <img :src="Logo" alt="Foodnow Logo" class="header__logo" />

    <!-- Menu Desktop -->
    <nav class="header__main-nav">
      <ul>
        <li><router-link to="/home">Home</router-link></li>
        <li><router-link to="/">Order</router-link></li>
        <li><router-link to="/about">About</router-link></li>
        <li><router-link to="/blog">Blog</router-link></li>
        <li><router-link to="/contactUs">Contact us</router-link></li>
      </ul>
    </nav>
    <!-- Login/Register Desktop-->
    <div class="header__secondary-nav">
      <router-link to="/login">Login</router-link>
      <button><router-link to="/register">Register</router-link></button>
    </div>

    <!-- Button for Mobile -->
    <nav class="header__main-nav--mobile">
      <button @click="isMobileMenuOpen = !isMobileMenuOpen">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="32"
          height="32"
          fill="#ffffff"
          class="bi bi-list"
          viewBox="0 0 16 16"
        >
          <path
            fill-rule="evenodd"
            d="M2.5 12a.5.5 0 0 1 .5-.5h10a.5.5 0 0 1 0 1H3a.5.5 0 0 1-.5-.5m0-4a.5.5 0 0 1 .5-.5h10a.5.5 0 0 1 0 1H3a.5.5 0 0 1-.5-.5m0-4a.5.5 0 0 1 .5-.5h10a.5.5 0 0 1 0 1H3a.5.5 0 0 1-.5-.5"
          />
        </svg>
      </button>
    </nav>

    <!-- Menu Mobile List -->
    <nav class="header__mobile-menu" :class="{ 'header__mobile-menu--open': isMobileMenuOpen }">
      <ul>
        <li><router-link to="/home">Home</router-link></li>
        <li><router-link to="/">Order</router-link></li>
        <li><router-link to="/about">About</router-link></li>
        <li><router-link to="/blog">Blog</router-link></li>
        <li><router-link to="/contactUs">Contact us</router-link></li>
      </ul>

      <div class="header__mobile-menu-buttons">
        <ul>
          <li><router-link to="/login">Login</router-link></li>
          <li>
            <button><router-link to="/register">Register</router-link></button>
          </li>
        </ul>
      </div>
    </nav>
  </header>
</template>
