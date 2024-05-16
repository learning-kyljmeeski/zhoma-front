<script>
    export let message = '';
    export let type = 'success'; // success, error

    let visible = true;

    function close() {
        visible = false;
    }
</script>

<style>
    .notification {
        position: fixed;
        top: 20px;
        right: 20px;
        padding: 15px 20px;
        background-color: var(--background-color);
        color: white;
        border-radius: 5px;
        box-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
        z-index: 1000;
        animation: bounceInRight 0.5s cubic-bezier(0.68, -0.55, 0.27, 1.55); /* Анимация появления */
    }

    .notification.success {
        --background-color: #28a745;
    }

    .notification.error {
        --background-color: #dc3545;
    }

    .close {
        margin-left: 15px;
        cursor: pointer;
    }

    @keyframes bounceInRight {
        0% {
            transform: translateX(300px) scaleY(2);
            opacity: 0;
        }
        60% {
            transform: translateX(-25px) scaleY(0.9);
            opacity: 1;
        }
        100% {
            transform: translateX(0) scaleY(1);
            opacity: 1;
        }
    }
</style>

{#if visible}
    <div class="notification {type}">
        {message}
        <span class="close" on:click={close}>✖</span>
    </div>
{/if}
