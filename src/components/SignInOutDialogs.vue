<!-- This component displays an authentication dialog overlay. 
     It uses the injected authDialogState to determine whether to show the dialog.
     When shown, the dialog contains a warning message that authentication has not been implemented yet, along with a button to close the dialog. 
-->

<script setup lang="ts">
import { DialogState } from '@/types/DialogState';
import { inject, computed } from 'vue';
import { useAuthStore } from '@/store/auth';
import { signOut } from 'aws-amplify/auth'

import { Authenticator } from "@aws-amplify/ui-vue";
import "@aws-amplify/ui-vue/styles.css";

import { Amplify } from 'aws-amplify';
import awsconfig from '../aws-exports';

Amplify.configure(awsconfig);

const authDialogSignOutState = inject<DialogState>('authDialogSignOutState', { showDialog: false });
const authDialogSignInState = inject<DialogState>('authDialogSignInState', { showDialog: false });

const authStore = useAuthStore();

function closeDialog() {
    authDialogSignOutState.showDialog = false;
    authDialogSignInState.showDialog = false;
}

function signIn() {
    signOut();
    authStore.userAuthenticated = true;
    authDialogSignInState.showDialog = false;
}

function signOutUser() {
    authStore.userAuthenticated = false;
    authDialogSignOutState.showDialog = false;
}
</script>

<template>
    <v-dialog v-model="authDialogSignInState.showDialog" width="unset" transition="dialog-top-transition">
        <authenticator>
            <template v-slot="{ user }">
                {{ signIn() }}
            </template>
        </authenticator>        
    </v-dialog>
    <v-dialog v-model="authDialogSignOutState.showDialog" width="unset" transition="dialog-top-transition">
        <v-card title="Sign Out?" color="warning">
            <v-card-text>
                Are you sure you want to sign out?
            </v-card-text>
            <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn text="Sign Out" @click="signOutUser"></v-btn>
                <v-btn text="Cancel" @click="closeDialog"></v-btn>
            </v-card-actions>
        </v-card>
    </v-dialog>
</template>
