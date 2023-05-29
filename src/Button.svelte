<script>
    import { spring } from 'svelte/motion';
    import { afterUpdate } from 'svelte';
    import { animate } from 'svelte/animate';
  
    let isClicked = false;
  
    const jiggle = () => {
      if (!isClicked) {
        isClicked = true;
        animate(
          document.getElementById('button'),
          {
            transform: 'rotate(-10deg)'
          },
          {
            duration: 200,
            easing: 'ease'
          }
        ).then(() => {
          animate(
            document.getElementById('button'),
            {
              transform: 'rotate(10deg)'
            },
            {
              duration: 400,
              easing: 'ease'
            }
          ).then(() => {
            animate(
              document.getElementById('button'),
              {
                transform: 'rotate(0)'
              },
              {
                duration: 200,
                easing: 'ease'
              }
            ).then(() => {
              isClicked = false;
            });
          });
        });
      }
    };
  </script>
  
  <style>
    button {
      padding: 8px 16px;
      border-radius: 4px;
    }
  </style>
  
  <button id="button" on:click={jiggle}>
    Click Me
  </button>
  