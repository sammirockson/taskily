<template>
  <div class="pricing">
    <NavBar :selectedIndex="2" @didSelectNavItem="didSelectNavItem"/>
    <div class="top-content-view">
        <img src="../assets/right-shape.png" class="right-shape">
        <div class="center-info-container">
                <label class="main-title-label">Affordable Options <br>for Every Business</label>
                <label class="main-sub-title">No credit card required.</label>
        </div>
        <img src="../assets/left-shape.png" class="right-shape">
   </div>

   <div class="content-view">
       <label class="plan-title">Find the Perfect Plan for Your Needs</label>
       <div class="monthly-yearly-payment">
        <label for="">Pay monthly</label>
        <!-- <v-switch v-model="isYearly" color="orange"></v-switch>
        <label for="">Pay yearly</label> -->
       </div>
       <div class="payment-options">
          <div class="payment-cell" v-for="option in options" :key="option.title">
            <label class="plan-type-label">{{ option.title }}</label>
            <label class="plan-subtitle">{{ option.subTitle }}</label>
            <label class="plan-title" v-if="option.title == 'Enterprise'">---</label>
            <label class="plan-title" v-else-if="option.price == 0">Free</label>
            <label class="plan-title" v-else-if="option.title == 'Business'">US${{option.price}}<span>/month</span></label>
            <label class="plan-title" v-else>US${{option.price}}<span>/month</span></label>
            <div class="line"></div>
            <div class="list">
                <div class="list-cell" v-for="list in option.lists">
                    <img src="../assets/check.png" class="check-icon">
                    <label>{{ list }}</label>
                </div>
            </div>
            <div class="line"></div>
            <button v-if="option.id === 'enterprise'" class="signup-btn" @click="handleNavToLogin(option)">Contact Support</button>
            <button v-else class="signup-btn" @click="handleNavToLogin(option)">Get Started</button>
          </div>
       </div>
   </div>
   <FooterView />
  </div>
</template>

<script>
import FooterView from '@/views/FooterView.vue';
import NavBar from '@/components/NavBar.vue';

export default {
    components: {
        FooterView, NavBar
    },
    mounted() {
        window.scrollTo(0, 0);
    //     setTimeout(() => {
    //      this.handleNavToLogin()
    //    }, 2000); 
    }, 
    data() {
        return {
            options: [
                {title: 'Basic', subTitle: 'For an individual or a team of two getting started', id: 'basic', price: 0, lists: [
                    'Limited to one board', 
                    'Limited to two members', 
                    'Unlimited lists', 
                    'Unlimited cards'
                ]}, // Free
                {title: 'Standard', subTitle: 'For a small team of five getting started', id: 'standard', price: 8, lists: [
                    'Team chat', 
                    'Team time tracking', 
                    'Limited to five members', 
                    'Unlimited boards', 
                    'Unlimited lists', 
                    'Unlimited Cards', 
                    'Card templates', 
                    'Private team and projects'
                ]}, // 5 per month
                {title: 'Business', subTitle: 'Mange teams, projects and capture employee time.', id: 'business', price: 15, lists: [
                    'Unlimited boards and cards',
                    'Dasbhoard', 
                    'Approvals', 
                    'Team chat', 
                    'Employee attendance tracking', 
                    'Card templates', 
                    'Custom field builder', 
                    'Advance integrations'
                ]},
                {title: 'Enterprise', subTitle: 'Mange teams, projects and capture employee time.', id: 'enterprise', price: 100.00, lists: [
                    'Unlimited boards and cards',
                    'Dasbhoard', 
                    'Approvals', 
                    'Team chat', 
                    'Employee attendance tracking', 
                    'Card templates', 
                    'Custom field builder', 
                    'Advance integrations'
                ]}, // 30 per month
            ], 
            isYearly: false, 
            subscriptionType: 'basic'
        }
    },
    methods: {
        handleNavToLogin(option) {
            console.log('option: ', option)
            this.subscriptionType = option.title.toLowerCase()
            // this.$router.push({path: '/pay'})
             let path = "/login"
            this.$router.push(
                {
                    path: path, 
                    query: {
                        subscription: this.subscriptionType
                    }
                })
        }, 
        didSelectNavItem(navItem) {
            if (navItem.id === 'templates') { // Template
                this.$router.push({path: '/templates'})
            } else if (navItem.id === 'features') { // Pricing
                this.$router.push({path: '/'})
            } else if (navItem.id === 'about') { // Pricing
                this.$router.push({path: '/about'})
            }
        }
    }
}
</script>

<style lang="scss"scoped>
.signup-btn {
    width: 180px;
    height: 44px;
    background-color: var(--color-bar-dark);
    border-radius: var(--border-radius-1);
    color: white;
    margin-left: auto;
    margin-right: auto;
}
.check-icon {
    width: 20px;
    height: 20px;
    margin-left: 15px;
    margin-right: 8px;
}
.list-cell {
    display: flex;
    align-items: center;
    height: 50px;
}
.list {
    display: flex;
    flex-direction: column;
    height: 360px;
}
.line {
    width: 90%;
    height: 1px;
    background-color: var(--color-dark);
    margin-right: auto;
    margin-left: auto;
}
.plan-title span {
    font-weight: 500;
    font-size: 10px;
    color: var(--color-dark);
}
.plan-subtitle {
    width: 80%;
    height: 80px;
    text-align: center;
    margin-right: auto;
    margin-left: auto;
}
.right-shape {
    object-fit: contain;
    height: 100%;   
}
.monthly-yearly-payment {
    display: flex;
    width: 300px;
    height: 50px;
    margin-right: auto;
    margin-left: auto;
    margin-top: 20px;
    margin-bottom: 30px;
    justify-content: center;
    gap: 20px;
}
.monthly-yearly-payment label {
    margin-top: 15px;
    font-weight: 600;
    font-size: 16px;
}
.content-view {
    display: flex;
    justify-content: center;
    flex-direction: column;
    margin-bottom: 200px;
}
.payment-cell {
    display: flex;
    flex-direction: column;
    height: 750px;
    width: 300px;
    gap: 20px;
    background-color: #f8f4f4;
    border-radius: var(--border-radius-3);
}
.payment-cell:hover {
    background-color: #ffcc44;
    border: 1px solid var(--color-dark);
}
.payment-options {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 30px;
}
.monthly-yearly-payment {
    display: flex;
}
.main-sub-title {
    font-weight: 500;
    font-size: 18px;
    color: var(--color-dark);
}
.center-info-container {
    display: flex;
    flex-direction: column;
    gap: 30px;
}
.main-title-label {
    font-weight: 800;
    font-size: 70px;
    padding-top: 10px;
    line-height: 80px;
}
.plan-title, .plan-type-label {
    font-weight: 700;
    font-size: 40px;
}
.plan-type-label {
    font-size: 24px;
    margin-top: 20px;
}
.top-content-view {
    display: flex;
    justify-content: space-between;
    height: 400px;
    width: 100vw;
    margin-top: 60px;
    margin-bottom: 30px;
    padding-top: 50px;
    background-color: var(--color-light-yellow);
}

.pricing {
    display: flex;
    flex-direction: column;
    width: 100vw;
    background-color: white;
}

</style>