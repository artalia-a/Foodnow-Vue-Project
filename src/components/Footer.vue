<script setup>
import Logo from '@/assets/images/logo-alt.svg'
import YouTube from '@/assets/images/icons/youtube.svg'
import Instagram from '@/assets/images/icons/instagram.svg'
import Facebook from '@/assets/images/icons/facebook.svg'
import Phone from '@/assets/images/icons/phone.svg'
import Email from '@/assets/images/icons/mail.svg'
import WhatsApp from '@/assets/images/icons/whatsapp.svg'
import Location from '@/assets/images/icons/location.svg'
</script>

<template>
  <footer class="footer">
    <div class="footer__top">
      <div class="footer__brand">
        <img :src="Logo" alt="Foodnow logo" class="footer__logo" />
        <p class="footer__text-title">Find Us:</p>
        <div class="footer__socials">
          <a href="#" target="_blank" class="footer__social-link">
            <img :src="Facebook" alt="Facebook" class="footer__social-icon" />
          </a>
          <a href="#" target="_blank" class="footer__social-link">
            <img :src="Instagram" alt="Instagram" class="footer__social-icon" />
          </a>
          <a href="#" target="_blank" class="footer__social-link">
            <img :src="YouTube" alt="YouTube" class="footer__social-icon" />
          </a>
        </div>
      </div>

      <div>
        <h4 class="footer__text-title">Navigation</h4>
        <ul class="footer__nav-list">
          <li><router-link to="/">Home</router-link></li>
          <li><router-link to="/contact">Contact</router-link></li>
          <li><router-link to="/order">Order</router-link></li>
          <li><router-link to="/">Login</router-link></li>
          <li><router-link to="/about">About</router-link></li>
          <li><router-link to="/">Register</router-link></li>
          <li><router-link to="/blog">Blog</router-link></li>
        </ul>
      </div>

      <div>
        <h4 class="footer__text-title">Contact</h4>
        <div class="footer__contact-item">
          <img :src="Email" alt="Email" />
          <span>food.now@mail.com</span>
        </div>
        <div class="footer__contact-item">
          <img :src="Phone" alt="Phone" />
          <span>+62 878 1234 5678</span>
        </div>
        <div class="footer__contact-item">
          <img :src="WhatsApp" alt="WhatApp" />
          <span>+62 878 1234 5678</span>
        </div>
      </div>

      <div>
        <h4 class="footer__text-title">Location</h4>
        <div class="footer__location">
          <div class="footer__location-header">
            <img :src="Location" alt="Location" />
            <span class="footer__location-text">Kerobokan</span>
          </div>
        </div>

        <p class="footer__location-address">
          Jl. Raya Kerobokan Br Taman, Kuta No.98, <br />
          Kerobokan Kelod, Kec. Kuta Utara, <br />
          Kabupaten Badung, Bali 80361
        </p>
      </div>
    </div>
    <div class="footer__bottom">
      <p>
        &copy; 2022 Foodnow. All Rights Reserved. Powered by PT. Timedoor Indonesia. |
        <span><a href="#">Privacy Policy</a></span>
      </p>
      <p class="footer__bottom-secondary">
        This site is protected by reCAPTCHA and the Google Privacy Policy and Term of Service apply
      </p>
    </div>
  </footer>
</template>
