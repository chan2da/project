# project
c언어 과제

코드


#define SDL_MAIN_HANDLED  // SDL_main을 사용하지 않도록 정의

#include <SDL.h>
#include <SDL_image.h>
#include <stdio.h>

int main(int argc, char* argv[]) {
    // SDL 초기화
    if (SDL_Init(SDL_INIT_VIDEO) != 0) {
        fprintf(stderr, "SDL_Init Error: %s\n", SDL_GetError());
        return 1;
    }

    // 이미지 라이브러리 초기화 (PNG, JPG 지원)
    if (IMG_Init(IMG_INIT_PNG | IMG_INIT_JPG) == 0) {
        fprintf(stderr, "IMG_Init Error: %s\n", IMG_GetError());
        SDL_Quit();
        return 1;
    }

    // 윈도우 생성
    SDL_Window* window = SDL_CreateWindow("Image Viewer",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED,
        800, 600, SDL_WINDOW_SHOWN);
    if (!window) {
        fprintf(stderr, "SDL_CreateWindow Error: %s\n", SDL_GetError());
        IMG_Quit();
        SDL_Quit();
        return 1;
    }

    // 렌더러 생성
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, SDL_RENDERER_ACCELERATED);
    if (!renderer) {
        fprintf(stderr, "SDL_CreateRenderer Error: %s\n", SDL_GetError());
        SDL_DestroyWindow(window);
        IMG_Quit();
        SDL_Quit();
        return 1;
    }

    // 이미지 파일 로드 (파일 경로 수정: "jcshim.png" 또는 "jcshim.jpg")
    const char* filename = "jcshim.png";  // 또는 "jcshim.jpg"로 변경
    SDL_Surface* img_surface = IMG_Load(filename);
    if (!img_surface) {
        fprintf(stderr, "IMG_Load Error: %s\n", IMG_GetError());
        SDL_DestroyRenderer(renderer);
        SDL_DestroyWindow(window);
        IMG_Quit();
        SDL_Quit();
        return 1;
    }

    // 텍스처로 변환
    SDL_Texture* img_texture = SDL_CreateTextureFromSurface(renderer, img_surface);
    SDL_FreeSurface(img_surface);  // Surface는 더 이상 필요 없음

    if (!img_texture) {
        fprintf(stderr, "SDL_CreateTextureFromSurface Error: %s\n", SDL_GetError());
        SDL_DestroyRenderer(renderer);
        SDL_DestroyWindow(window);
        IMG_Quit();
        SDL_Quit();
        return 1;
    }

    // 렌더링 루프
    int quit = 0;
    SDL_Event e;
    while (!quit) {
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) {
                quit = 1;
            }
        }

        // 화면 지우기
        SDL_RenderClear(renderer);

        // 이미지 렌더링
        SDL_RenderCopy(renderer, img_texture, NULL, NULL);

        // 화면에 그리기
        SDL_RenderPresent(renderer);
    }

    // 자원 해제
    SDL_DestroyTexture(img_texture);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);

    // 라이브러리 종료
    IMG_Quit();
    SDL_Quit();

    return 0;
}

