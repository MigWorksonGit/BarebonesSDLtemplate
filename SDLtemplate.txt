#include <SDL2/SDL.h>

#define SW 384
#define SH 216
#define TRUE 1
#define FALSE 0

SDL_Window*     window;
SDL_Renderer*   renderer;
SDL_Texture*    texture;
unsigned int    pixels[SW * SH] = {0};
char            quit = FALSE;

/* cap frame rate */
const int   DESIRED_FPS = 30;
const int   TARGET_INTERVAL = 1000 / DESIRED_FPS;
int         frame_timer = 0;
static void frame_rate()
{
    int current_time = SDL_GetTicks();
    int frame_duration = current_time - frame_timer;
    frame_timer = current_time;
    if (frame_duration < TARGET_INTERVAL)
    {
        int delay = TARGET_INTERVAL - frame_duration;
        SDL_Delay(delay);
    }
}

int main(void)
{
    SDL_Init(SDL_INIT_VIDEO);
    window = SDL_CreateWindow(
        "Hello World",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED,
        1280, 720, SDL_WINDOW_SHOWN
    );
    renderer = SDL_CreateRenderer(window, -1, 0);
    texture  = SDL_CreateTexture(
        renderer,
        SDL_PIXELFORMAT_ABGR8888, SDL_TEXTUREACCESS_STREAMING,
        SW, SH
    );

    while (!quit)
    {
        SDL_Event event;
        while (SDL_PollEvent(&event))
        {
            if (event.type == SDL_QUIT) quit = TRUE;
        }
        if (quit) break;

        const unsigned char* keystate = SDL_GetKeyboardState(NULL);
        if (keystate[SDLK_RIGHT & 0x0FFF]) {
            /* do stuff */
        }

        memset(pixels, 0, sizeof(pixels));
        /* render */

        SDL_UpdateTexture(texture, NULL, pixels, SW * 4);
        SDL_RenderCopy(renderer, texture, NULL, NULL);
        SDL_RenderPresent(renderer);

        frame_rate();
    }

    SDL_DestroyTexture(texture);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
