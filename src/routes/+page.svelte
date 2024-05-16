<script>
    import { onMount } from 'svelte';
    import Notification from '$lib/Notification.svelte';
    
    let phoneNumber = '';
    let message = '';
    let notificationMessage = '';
    let notificationType = 'success';
    let showNotification = false;
    let loading = false;
    let phoneNumberErrorText = ' ';
    let messageErrorText = ' ';

    function handlePhoneInput(event) {
        // Получаем значение из поля ввода
        const inputValue = event.target.value;
        
        // Обновляем переменную phoneNumber, добавляя к ней значение из поля ввода
        phoneNumber = '+996' + inputValue;
    }

    async function makeCall() {
        if (phoneNumber.length != 13) {
            phoneNumberErrorText = 'I need the real number';
            return;
        } else {
            phoneNumberErrorText = ' ';
        }

        if (message.trim().length === 0) {
            messageErrorText = 'Write something :)'
            return;
        } else {
            messageErrorText = ' ';
        }

        loading = true;

        try {
            const response = await fetch(`http://localhost:8080/api/call?to=${phoneNumber}&message=${message}`, {
                method: 'POST',
            });

            if (response.ok) {
                const data = await response.text();
                notificationMessage = 'The call was made';
                notificationType = 'success';
                console.log(data)
            } else {
                const errorData = await response.json();
                notificationMessage = errorData.error || 'Error when making a call';
                notificationType = 'error';
                console.error('Error when making a call:', errorData);
            }
        } catch (error) {
            notificationMessage = 'Network error: ' + error.message;
            notificationType = 'error';
            console.error('Network error:', error);
        } finally {
            loading = false;
            showNotification = true;
            setTimeout(() => {
                showNotification = false;
            }, 3000);
            message = '';
            phoneNumberErrorText = ' ';
            messageErrorText = ' '
        }
    }
</script>

<style>
    .container {
    max-width: 500px;
    margin: 0 auto;
    padding: 20px;
    text-align: center;
    font-family: "Arial", sans-serif;
    background-color: #f9f9f9;
    border: 1px solid #e0e0e0;
    border-radius: 10px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

input, textarea {
    border-radius: 8px;
    border: 2px solid #007bff;
    outline: none;
    transition: border-color 0.3s ease;
}

input:focus, textarea:focus {
    border-color: #0056b3;
}

.input-group {
    display: flex;
    flex-direction: column;
    margin-bottom: 15px;
}

.input-group .phone-input-group {
    display: flex;
}

.input-group .phone-input-group input:first-child {
    width: 110px;
    text-align: center;
    border-top-right-radius: 0;
    border-bottom-right-radius: 0;
}

.input-group .phone-input-group input:nth-child(2) {
    border-top-left-radius: 0;
    border-bottom-left-radius: 0;
    border-left: 0;
}

.input-group span.error-message {
    min-height: 1.3em;
    margin-top: 5px;
    font-size: 12px;
    color: #d9534f;
    margin-right: auto;
    font-family: "Arial", sans-serif;
}

label {
    display: block;
    margin-bottom: 5px;
    font-weight: bold;
    color: #333;
}

input, textarea {
    font-family: "Arial", sans-serif;
    width: 100%;
    padding: 12px;
    font-size: 16px;
    box-sizing: border-box;
    background-color: #fff;
    color: #333;
}

button {
    margin-top: 15px;
    font-family: "Arial", sans-serif;
    padding: 12px 20px;
    font-size: 16px;
    background-color: #28a745;
    color: white;
    border: none;
    cursor: pointer;
    width: 210px;
    border-radius: 8px;
    transition: background-color 0.3s ease, transform 0.2s ease;
}

button:hover {
    background-color: #218838;
    transform: scale(1.05);
}

</style>

<div class="container">
    <div class="input-group">
        <label for="phone">Phone</label>
        <input type="tel" id="phone" on:input={handlePhoneInput} placeholder="+996704646426" value="+996704646426" autocomplete="off"/>
    </div>
    <div class="input-group">
        <label for="message">Message</label>
        <textarea id="message" bind:value={message} placeholder="Message text"></textarea>
    </div>
    <button on:click={makeCall} disabled={loading}> <!-- Добавим disabled для блокировки кнопки во время загрузки -->
        {#if loading}
            Working on it ...
        {:else}
            Call
        {/if}
    </button>
</div>
