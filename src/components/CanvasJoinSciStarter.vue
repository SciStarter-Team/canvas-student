<template>
  <form class="createAccount" @submit.prevent="create">
    <label class="label">School email</label>
    <input v-if="allowPickEmail" type="email" v-model="signup_email">
    <strong v-else>{{user.email}}</strong>
    <label class="label">Password</label>
    <input type="password" v-model="signup_password"/>
    <a v-if="allowSkip" style="margin-right: 1rem" @click="cancel">skip</a><button type="submit" class="cbtn-primary"><i ref="join_spinner"></i>Create SciStarter Account</button>
  </form>
</template>

<script>
export default {
    name:'JoinSciStarter',
    props: ['user', 'allowSkip', 'allowPickEmail', 'next'],
    data: function() {
        return {
            signup_email: "",
            signup_password: "",
        }
    },
    computed: {
        xcsrftoken() {
            return JSON.parse(document.getElementById('data-xcsrf-token').textContent);
        }
    },
    methods: {
        cancel() {
            this.$emit('close');
        },

        create() {
            var ctx = this;

            var email = ctx.signup_email ? ctx.signup_email : ctx.user.email;

            var body = new FormData();
            body.append("email", email);
            body.append("password", ctx.signup_password);

            ctx.$refs.join_spinner.classList.add("spinner-border", "spinner-border-sm");

            fetch(ctx.user.create_account, {
                method: "POST",
                credentials: "include",
                headers: {"X-XCSRFToken": ctx.xcsrftoken},
                body: body
            }).then(response => response.json()).then(function(data) {
                ctx.$refs.join_spinner.classList.remove("spinner-border", "spinner-border-sm");

                if(data.result == 'created' || data.result == 'linked') {
                    ctx.user.email = data.email;
                    ctx.user.hasAccount = true;

                    if(data.warning) {
                        alert(data.warning);
                    }
                    else {
                        this.$emit('close');
                        if(ctx.next) {
                            window.open(ctx.next, '_blank');
                        }
                    }
                }
                else {
                    alert(data.error);
                }
            });
        }
    }
}
</script>

<style lang="scss" scoped>
.createAccount {
    width: 100%;
    margin: 0 auto;
    padding: 1rem;
    border-radius: 8px;
    border: 4px solid #47A8D4;
    box-shadow: 2px 0 6px rgba(0,0,0,.5);
    label {
        margin-top: 0.25em;
        display: block;
    }
    input {
        width: 100%;
        display: block;
        border: 1px solid #ccc;
        padding: .4rem;
    }
}

.cbtn-primary > i {
    vertical-align: middle;
}
</style>
