<template>
    <v-form v-model="valid">
      <v-card max-width="400" class="mx-auto pa-5">
        <v-card-title class="card-header">
          New Message
        </v-card-title>
        <v-row>
          <v-col
            cols="12"
            md="12"
          >
            <v-text-field
              v-model="recipient"
              :rules="recipientRules"
              label="Recipient"
              required
            ></v-text-field>
          </v-col>

          <v-col
            cols="12"
            md="12"
          >
            <v-text-field
              v-model="subject"
              :rules="subjectRules"
              label="Subject"
              required
            ></v-text-field>
          </v-col>

          <v-col
            cols="12"
            md="12"
            class="message-container"
          >
            <v-textarea
              tabindex="-1"
              class="suggestion-textarea py-6 px-3"
              auto-grow
              v-model="suggestMessage"
            ></v-textarea>
            <v-textarea
              v-model="message"
              auto-grow
              :rules="messageRules"
              @keydown="tabButtonSuggest"
              required
            ></v-textarea>
          </v-col>
        </v-row>
        <v-progress-circular
          indeterminate
          color="primary"
          v-if="sending"
        ></v-progress-circular>
        <v-card-actions class="px-0 mt-3">
          <v-btn
            type="submit"
            class="md-primary"
            @click="sendMessage()"
            :disabled="sending"
          >
            Send message
          </v-btn>
        </v-card-actions>
      </v-card>
      <v-snackbar :md-active.sync="messageSent">The message was sent with success!</v-snackbar>
    </v-form>
</template>

<script>
  export default {
    data: () => ({
      sending: false,
      suggestions: [
        "Do you have time to meet next week?",
        "I have forwarded this message to the appropriate service rep.",
        "If you're not the right person, can you please put me in touch with whoever is?",
        "Thanks again for chatting today and I look forward to hearing from you!",
      ],
      messageSent: false,
      valid: false,
      subject: '',
      subjectRules: [
        v => !!v || 'Subject is required',
        v => /.+@.+/.test(v) || 'Recipient must be valid',
      ],
      message: '',
      messageRules: [
        v => !!v || 'Message is required',
      ],
      recipient: '',
      recipientRules: [
        v => !!v || 'Recipient is required',
        v => /.+@.+/.test(v) || 'Recipient must be valid',
      ],
    }),
    computed: {
      suggestMessage: function() {
        if(this.message.length) {
          return this.findSuggestion();
        }
        return this.suggestions[0];
      },
    },
    methods: {
      findSuggestion (isOriginalString) {
        let string = '';
        for(let suggestion of this.suggestions) {
          const mainString = this.message;
          let suggestionString = suggestion;
          let incorrect = false;
          let count = 0;
          for(let i of mainString) {
            if(incorrect) break;
            if(i.toLowerCase() !== suggestion[count].toLowerCase()) {
              incorrect = true;
            }
            suggestionString = suggestionString.slice(0, count) + i + suggestionString.slice(count+1, suggestionString.length);
            count++;
          }
          if(!incorrect) {
            if(isOriginalString) {
              return suggestion;
            }
            return suggestionString;
          }
        }
        return string;
      },
      tabButtonSuggest (e) {
        if(e.key.toLowerCase() === 'tab') {
          e.preventDefault();
          const suggestionMessage = this.findSuggestion(true);
          if(!suggestionMessage.length) return;
          let isSpace = false;
          let count = this.message.length;
          while(!isSpace && count < 50) {
            if((suggestionMessage[count] === ' ' && count !== this.message.length) || suggestionMessage[count] === undefined) {
              isSpace = true;
            }
            count++;
          }
          const space = suggestionMessage[count+1] === ' ' && suggestionMessage[count+2] !== undefined ? ' ' : '';
          this.message = `${suggestionMessage.slice(0, count)}${space}`;
        }
      },
      sendMessage () {
        this.sending = true

        // Instead of this timeout, here you can call your API
        window.setTimeout(() => {
          this.messageSent = true;
          this.sending = false;
        }, 1500)
      },
    }
  }
</script>

<style lang="scss">
  .suggestion-textarea {
    position: absolute;
    top: 0;
    left: 0;
    opacity: 0.5;
    border: none;
    width: 100%;

    & .v-input__slot {
      &:before, &:after {
        display: none;
      }
    }
  }
  .message-container {
    position: relative;
  }
  .card-header {
    background: #444444;
    color: white;
    margin: -20px -20px 20px -20px;
  }
</style>
