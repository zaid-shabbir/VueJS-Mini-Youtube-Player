<template>
  <div>
    <div class="wrapper">
      <div class="player">
        <div class="player__top">
          <div class="player-cover">
            <div class="video">
              <img
                v-if="!playing"
                src="../assets/images/cover.jpg"
                class="default-image"
                alt="Default Image"
              />
              <div id="youtube-player"></div>
            </div>
          </div>
          <div class="player-controls">
            <div
              class="player-controls__item -favorite next"
              :class="{ active: current_video.favorite }"
              @click="favorite"
            >
              <svg class="icon">
                <use xlink:href="#icon-heart-o"></use>
              </svg>
            </div>
            <div class="player-controls__item next" @click="nextTrack">
              <svg class="icon">
                <use xlink:href="#icon-next"></use>
              </svg>
            </div>
            <div class="player-controls__item prev" @click="prevTrack">
              <svg class="icon">
                <use xlink:href="#icon-prev"></use>
              </svg>
            </div>
            <div class="player-controls__item -xl js-play play" @click="play">
              <svg class="icon">
                <use xlink:href="#icon-pause" v-if="isTimerPlaying"></use>
                <use xlink:href="#icon-play" v-else></use>
              </svg>
            </div>
          </div>
        </div>
        <div class="progress" ref="progress">
          <div class="album-info__name">{{ current_video.title }}</div>
          <div class="progress__top">
            <div class="progress__duration">{{ current_video.duration }}</div>
          </div>
          <div class="progress__bar" @click="clickProgress">
            <div class="progress__current" :style="{ width: barWidth }"></div>
          </div>
          <div v-if="!currentTime" class="progress__time">00:00</div>
          <div v-if="currentTime" class="progress__time">{{ currentTime }}</div>
        </div>
        <div class="add_link">
          <label class="youtube_link_label" for="youtube_link"
            >FREE: Add A Youtube Video</label
          >
          <div class="add_block">
            <input
              v-model="url"
              class="youtube_link_input"
              type="text"
              id="youtube_link"
              placeholder="https://..Paste Your Youtube URL Link here"
              @keyup.enter="addInPlaylist"
            />
            <svg
              @click="addInPlaylist"
              aria-hidden="true"
              focusable="false"
              data-prefix="fas"
              data-icon="plus-circle"
              class="svg-inline--fa fa-plus-circle fa-w-16 add_svg"
              role="img"
              height="35"
              width="35"
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 512 512"
            >
              <path
                fill="currentColor"
                d="M256 8C119 8 8 119 8 256s111 248 248 248 248-111 248-248S393 8 256 8zm144 276c0 6.6-5.4 12-12 12h-92v92c0 6.6-5.4 12-12 12h-56c-6.6 0-12-5.4-12-12v-92h-92c-6.6 0-12-5.4-12-12v-56c0-6.6 5.4-12 12-12h92v-92c0-6.6 5.4-12 12-12h56c6.6 0 12 5.4 12 12v92h92c6.6 0 12 5.4 12 12v56z"
              ></path>
            </svg>
          </div>
        </div>
        <div class="vip">
          Add my video to the
          <span class="vip-list">
            VIP QueList
          </span>
          &nbsp; for $1.00
          <input type="checkbox" id="checkbox" checked />
        </div>
      </div>
      <transition name="slide">
        <div v-if="show_playlist && !note" class="playlist">
          <div class="next-video-title">Next Video</div>
          <div class="video-detail">
            <div v-if="playlist.length > 1">
              <div
                v-if="current_video.number < playlist.length"
                class="video-title"
              >
                {{ playlist[current_video.number].number }}.&nbsp;{{
                  playlist[current_video.number].title
                }}
              </div>
              <div v-else class="video-title">
                {{ playlist[0].number }}.&nbsp;{{ playlist[0].title }}
              </div>
            </div>
            <div v-if="playlist.length > 1" class="video-duration-container">
              <div
                v-if="current_video.number < playlist.length"
                class="video-duration"
              >
                {{ playlist[current_video.number].duration }}
              </div>
              <div v-else class="video-duration">
                {{ playlist[0].duration }}
              </div>
              <svg
                height="20"
                width="10"
                aria-hidden="true"
                focusable="false"
                data-prefix="fal"
                data-icon="ellipsis-v"
                class="ellipses svg-inline--fa fa-ellipsis-v fa-w-2"
                role="img"
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 64 512"
              >
                <path
                  fill="currentColor"
                  d="M32 224c17.7 0 32 14.3 32 32s-14.3 32-32 32-32-14.3-32-32 14.3-32 32-32zM0 136c0 17.7 14.3 32 32 32s32-14.3 32-32-14.3-32-32-32-32 14.3-32 32zm0 240c0 17.7 14.3 32 32 32s32-14.3 32-32-14.3-32-32-32-32 14.3-32 32z"
                ></path>
              </svg>
            </div>
          </div>
          <div
            id="style-1"
            class="que-list-container"
            v-if="playlist.length > 1"
          >
            <div class="que-title">QueList</div>
            <div v-for="(video, index) in playlist" :key="index">
              <div v-if="playlist.length > 1" class="video-detail">
                <div
                  class="video-title"
                  :class="{
                    active: video.number == playlist[playlist.length - 1].number
                  }"
                >
                  {{ video.number }}.&nbsp;{{ video.title }}
                </div>
                <div class="video-duration-container">
                  <div
                    class="video-duration"
                    :class="{
                      active:
                        video.number == playlist[playlist.length - 1].number
                    }"
                  >
                    {{ video.duration }}
                  </div>
                  <svg
                    height="20"
                    width="10"
                    aria-hidden="true"
                    focusable="false"
                    data-prefix="fal"
                    data-icon="ellipsis-v"
                    class="ellipses svg-inline--fa fa-ellipsis-v fa-w-2"
                    role="img"
                    xmlns="http://www.w3.org/2000/svg"
                    viewBox="0 0 64 512"
                  >
                    <path
                      fill="currentColor"
                      d="M32 224c17.7 0 32 14.3 32 32s-14.3 32-32 32-32-14.3-32-32 14.3-32 32-32zM0 136c0 17.7 14.3 32 32 32s32-14.3 32-32-14.3-32-32-32-32 14.3-32 32zm0 240c0 17.7 14.3 32 32 32s32-14.3 32-32-14.3-32-32-32-32 14.3-32 32z"
                    ></path>
                  </svg>
                </div>
              </div>
              <hr v-if="playlist.length > 1" class="playlist-hr" />
            </div>
          </div>

          <div class="vip-section">
            <div>
              <div class="vip-top">
                Get in the <span class="vip-que">VIP QueList</span>
              </div>
              <div class="vip-bottom">
                Jump the line for only $1.00
              </div>
            </div>
            <button class="priority-btn">Add Priority</button>
          </div>
        </div>
      </transition>
      <div v-if="note" class="notification-container">
        <svg
          aria-hidden="true"
          focusable="false"
          data-prefix="fal"
          data-icon="list-alt"
          height="40"
          width="40"
          class="list-svg svg-inline--fa fa-list-alt fa-w-16"
          role="img"
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 512 512"
        >
          <path
            fill="currentColor"
            d="M464 64c8.823 0 16 7.178 16 16v352c0 8.822-7.177 16-16 16H48c-8.823 0-16-7.178-16-16V80c0-8.822 7.177-16 16-16h416m0-32H48C21.49 32 0 53.49 0 80v352c0 26.51 21.49 48 48 48h416c26.51 0 48-21.49 48-48V80c0-26.51-21.49-48-48-48zm-336 96c-17.673 0-32 14.327-32 32s14.327 32 32 32 32-14.327 32-32-14.327-32-32-32zm0 96c-17.673 0-32 14.327-32 32s14.327 32 32 32 32-14.327 32-32-14.327-32-32-32zm0 96c-17.673 0-32 14.327-32 32s14.327 32 32 32 32-14.327 32-32-14.327-32-32-32zm288-148v-24a6 6 0 0 0-6-6H198a6 6 0 0 0-6 6v24a6 6 0 0 0 6 6h212a6 6 0 0 0 6-6zm0 96v-24a6 6 0 0 0-6-6H198a6 6 0 0 0-6 6v24a6 6 0 0 0 6 6h212a6 6 0 0 0 6-6zm0 96v-24a6 6 0 0 0-6-6H198a6 6 0 0 0-6 6v24a6 6 0 0 0 6 6h212a6 6 0 0 0 6-6z"
          ></path>
        </svg>
        <div class="note">{{ note }}</div>
        <svg
          @click="removeNote"
          aria-hidden="true"
          focusable="false"
          data-prefix="fal"
          data-icon="times"
          height="26"
          width="26"
          class="times-svg svg-inline--fa fa-times fa-w-10"
          role="img"
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 320 512"
        >
          <path
            fill="currentColor"
            d="M193.94 256L296.5 153.44l21.15-21.15c3.12-3.12 3.12-8.19 0-11.31l-22.63-22.63c-3.12-3.12-8.19-3.12-11.31 0L160 222.06 36.29 98.34c-3.12-3.12-8.19-3.12-11.31 0L2.34 120.97c-3.12 3.12-3.12 8.19 0 11.31L126.06 256 2.34 379.71c-3.12 3.12-3.12 8.19 0 11.31l22.63 22.63c3.12 3.12 8.19 3.12 11.31 0L160 289.94 262.56 392.5l21.15 21.15c3.12 3.12 8.19 3.12 11.31 0l22.63-22.63c3.12-3.12 3.12-8.19 0-11.31L193.94 256z"
          ></path>
        </svg>
      </div>
      <svg
        v-if="!note"
        @click="togglePlaylist"
        class="playlist-toggler"
        width="246px"
        height="30px"
        viewBox="0 0 246 30"
        version="1.1"
        xmlns="http://www.w3.org/2000/svg"
        xmlns:xlink="http://www.w3.org/1999/xlink"
      >
        <title>Group</title>
        <g
          id="Page-1"
          stroke="none"
          stroke-width="1"
          fill="none"
          fill-rule="evenodd"
        >
          <g id="#121436" transform="translate(-189.000000, -407.000000)">
            <g id="Group" transform="translate(189.000000, 407.000000)">
              <path
                d="M25,0 L221,0 C225.602373,9.30914651e-16 229.333333,3.73096042 229.333333,8.33333333 C229.333333,10.9563109 228.098382,13.4262135 226,15 L208.4,28.2 C206.842134,29.3683992 204.947332,30 203,30 L43,30 C41.0526681,30 39.1578655,29.3683992 37.6,28.2 L20,15 C16.3181017,12.2385763 15.5719096,7.01523167 18.3333333,3.33333333 C19.9071198,1.23495131 22.3770225,2.25818999e-15 25,0 Z"
                id="Rectangle"
                fill="#121436"
              ></path>
              <rect
                id="Rectangle"
                fill-opacity="0.2"
                fill="#FFFFFF"
                x="72"
                y="20"
                width="102"
                height="5"
                rx="2.5"
              ></rect>
            </g>
          </g>
        </g>
      </svg>
      <!-- <div v-if="!note" @click="togglePlaylist" class="playlist-toggler">
        <hr class="see-playlist" />
      </div> -->
    </div>
    <svg
      xmlns="http://www.w3.org/2000/svg"
      hidden
      xmlns:xlink="http://www.w3.org/1999/xlink"
    >
      <defs>
        <symbol id="icon-heart-o" viewBox="0 0 32 32">
          <title>icon-heart-o</title>
          <path
            d="M22.88 1.952c-2.72 0-5.184 1.28-6.88 3.456-1.696-2.176-4.16-3.456-6.88-3.456-4.48 0-9.024 3.648-9.024 10.592 0 7.232 7.776 12.704 15.072 17.248 0.256 0.16 0.544 0.256 0.832 0.256s0.576-0.096 0.832-0.256c7.296-4.544 15.072-10.016 15.072-17.248 0-6.944-4.544-10.592-9.024-10.592zM16 26.56c-4.864-3.072-12.736-8.288-12.736-14.016 0-5.088 3.040-7.424 5.824-7.424 2.368 0 4.384 1.504 5.408 4.032 0.256 0.608 0.832 0.992 1.472 0.992s1.248-0.384 1.472-0.992c1.024-2.528 3.040-4.032 5.408-4.032 2.816 0 5.824 2.304 5.824 7.424 0.064 5.728-7.808 10.976-12.672 14.016z"
          ></path>
          <path
            d="M16 30.144c-0.32 0-0.64-0.096-0.896-0.256-7.296-4.576-15.104-10.048-15.104-17.344 0-7.008 4.576-10.688 9.12-10.688 2.656 0 5.152 1.216 6.88 3.392 1.728-2.144 4.224-3.392 6.88-3.392 4.544 0 9.12 3.68 9.12 10.688 0 7.296-7.808 12.768-15.104 17.344-0.256 0.16-0.576 0.256-0.896 0.256zM9.12 2.048c-4.448 0-8.928 3.616-8.928 10.496 0 7.168 7.744 12.64 15.008 17.152 0.48 0.288 1.12 0.288 1.568 0 7.264-4.544 15.008-9.984 15.008-17.152 0-6.88-4.48-10.496-8.928-10.496-2.656 0-5.088 1.216-6.816 3.392l-0.032 0.128-0.064-0.096c-1.696-2.176-4.192-3.424-6.816-3.424zM16 26.688l-0.064-0.032c-3.808-2.4-12.768-8.032-12.768-14.112 0-5.152 3.072-7.52 5.952-7.52 2.432 0 4.48 1.536 5.504 4.096 0.224 0.576 0.768 0.928 1.376 0.928s1.152-0.384 1.376-0.928c1.024-2.56 3.072-4.096 5.504-4.096 2.848 0 5.952 2.336 5.952 7.52 0 6.080-8.96 11.712-12.768 14.112l-0.064 0.032zM9.12 5.248c-2.752 0-5.728 2.304-5.728 7.328 0 5.952 8.8 11.488 12.608 13.92 3.808-2.4 12.608-7.968 12.608-13.92 0-5.024-2.976-7.328-5.728-7.328-2.336 0-4.32 1.472-5.312 3.968-0.256 0.64-0.864 1.056-1.568 1.056s-1.312-0.416-1.568-1.056c-0.992-2.496-2.976-3.968-5.312-3.968z"
          ></path>
          <path
            d="M6.816 20.704c0.384 0.288 0.512 0.704 0.48 1.12 0.224 0.256 0.384 0.608 0.384 0.96 0 0.032 0 0.032 0 0.064 0.16 0.128 0.32 0.256 0.48 0.384 0.128 0.064 0.256 0.16 0.384 0.256 0.096 0.064 0.192 0.16 0.256 0.224 0.8 0.576 1.632 1.12 2.496 1.664 0.416 0.128 0.8 0.256 1.056 0.32 1.984 0.576 4.064 0.8 6.112 0.928 2.688-1.92 5.312-3.904 8-5.792 0.896-1.088 1.92-2.080 2.912-3.104v-7.552c-0.096-0.128-0.192-0.288-0.32-0.416-0.768-1.024-1.184-2.176-1.6-3.296-0.768-0.416-1.536-0.8-2.336-1.12-0.128-0.064-0.256-0.096-0.384-0.16h-21.568v12.992c1.312 0.672 2.496 1.6 3.648 2.528z"
          ></path>
        </symbol>
        <symbol id="icon-heart" viewBox="0 0 32 32">
          <title>icon-heart</title>
          <path
            d="M22.88 1.952c-2.72 0-5.184 1.28-6.88 3.456-1.696-2.176-4.16-3.456-6.88-3.456-4.48 0-9.024 3.648-9.024 10.592 0 7.232 7.776 12.704 15.072 17.248 0.256 0.16 0.544 0.256 0.832 0.256s0.576-0.096 0.832-0.256c7.296-4.544 15.072-10.016 15.072-17.248 0-6.944-4.544-10.592-9.024-10.592zM16 26.56c-4.864-3.072-12.736-8.288-12.736-14.016 0-5.088 3.040-7.424 5.824-7.424 2.368 0 4.384 1.504 5.408 4.032 0.256 0.608 0.832 0.992 1.472 0.992s1.248-0.384 1.472-0.992c1.024-2.528 3.040-4.032 5.408-4.032 2.816 0 5.824 2.304 5.824 7.424 0.064 5.728-7.808 10.976-12.672 14.016z"
          ></path>
          <path
            d="M16 30.144c-0.32 0-0.64-0.096-0.896-0.256-7.296-4.576-15.104-10.048-15.104-17.344 0-7.008 4.576-10.688 9.12-10.688 2.656 0 5.152 1.216 6.88 3.392 1.728-2.144 4.224-3.392 6.88-3.392 4.544 0 9.12 3.68 9.12 10.688 0 7.296-7.808 12.768-15.104 17.344-0.256 0.16-0.576 0.256-0.896 0.256zM9.12 2.048c-4.448 0-8.928 3.616-8.928 10.496 0 7.168 7.744 12.64 15.008 17.152 0.48 0.288 1.12 0.288 1.568 0 7.264-4.544 15.008-9.984 15.008-17.152 0-6.88-4.48-10.496-8.928-10.496-2.656 0-5.088 1.216-6.816 3.392l-0.032 0.128-0.064-0.096c-1.696-2.176-4.192-3.424-6.816-3.424zM16 26.688l-0.064-0.032c-3.808-2.4-12.768-8.032-12.768-14.112 0-5.152 3.072-7.52 5.952-7.52 2.432 0 4.48 1.536 5.504 4.096 0.224 0.576 0.768 0.928 1.376 0.928s1.152-0.384 1.376-0.928c1.024-2.56 3.072-4.096 5.504-4.096 2.848 0 5.952 2.336 5.952 7.52 0 6.080-8.96 11.712-12.768 14.112l-0.064 0.032zM9.12 5.248c-2.752 0-5.728 2.304-5.728 7.328 0 5.952 8.8 11.488 12.608 13.92 3.808-2.4 12.608-7.968 12.608-13.92 0-5.024-2.976-7.328-5.728-7.328-2.336 0-4.32 1.472-5.312 3.968-0.256 0.64-0.864 1.056-1.568 1.056s-1.312-0.416-1.568-1.056c-0.992-2.496-2.976-3.968-5.312-3.968z"
          ></path>
        </symbol>
        <symbol id="icon-pause" viewBox="0 0 32 32">
          <title>icon-pause</title>
          <path
            d="M16 0.32c-8.64 0-15.68 7.040-15.68 15.68s7.040 15.68 15.68 15.68 15.68-7.040 15.68-15.68-7.040-15.68-15.68-15.68zM16 29.216c-7.296 0-13.216-5.92-13.216-13.216s5.92-13.216 13.216-13.216 13.216 5.92 13.216 13.216-5.92 13.216-13.216 13.216z"
          ></path>
          <path
            d="M16 32c-8.832 0-16-7.168-16-16s7.168-16 16-16 16 7.168 16 16-7.168 16-16 16zM16 0.672c-8.448 0-15.328 6.88-15.328 15.328s6.88 15.328 15.328 15.328c8.448 0 15.328-6.88 15.328-15.328s-6.88-15.328-15.328-15.328zM16 29.568c-7.488 0-13.568-6.080-13.568-13.568s6.080-13.568 13.568-13.568c7.488 0 13.568 6.080 13.568 13.568s-6.080 13.568-13.568 13.568zM16 3.104c-7.104 0-12.896 5.792-12.896 12.896s5.792 12.896 12.896 12.896c7.104 0 12.896-5.792 12.896-12.896s-5.792-12.896-12.896-12.896z"
          ></path>
          <path
            d="M12.16 22.336v0c-0.896 0-1.6-0.704-1.6-1.6v-9.472c0-0.896 0.704-1.6 1.6-1.6v0c0.896 0 1.6 0.704 1.6 1.6v9.504c0 0.864-0.704 1.568-1.6 1.568z"
          ></path>
          <path
            d="M19.84 22.336v0c-0.896 0-1.6-0.704-1.6-1.6v-9.472c0-0.896 0.704-1.6 1.6-1.6v0c0.896 0 1.6 0.704 1.6 1.6v9.504c0 0.864-0.704 1.568-1.6 1.568z"
          ></path>
        </symbol>
        <symbol id="icon-play" viewBox="0 0 32 32">
          <title>icon-play</title>
          <path
            d="M21.216 15.168l-7.616-5.088c-0.672-0.416-1.504 0.032-1.504 0.832v10.176c0 0.8 0.896 1.248 1.504 0.832l7.616-5.088c0.576-0.416 0.576-1.248 0-1.664z"
          ></path>
          <path
            d="M13.056 22.4c-0.224 0-0.416-0.064-0.608-0.16-0.448-0.224-0.704-0.672-0.704-1.152v-10.176c0-0.48 0.256-0.928 0.672-1.152s0.928-0.224 1.344 0.064l7.616 5.088c0.384 0.256 0.608 0.672 0.608 1.088s-0.224 0.864-0.608 1.088l-7.616 5.088c-0.192 0.16-0.448 0.224-0.704 0.224zM13.056 10.272c-0.096 0-0.224 0.032-0.32 0.064-0.224 0.128-0.352 0.32-0.352 0.576v10.176c0 0.256 0.128 0.48 0.352 0.576 0.224 0.128 0.448 0.096 0.64-0.032l7.616-5.088c0.192-0.128 0.288-0.32 0.288-0.544s-0.096-0.416-0.288-0.544l-7.584-5.088c-0.096-0.064-0.224-0.096-0.352-0.096z"
          ></path>
          <path
            d="M16 0.32c-8.64 0-15.68 7.040-15.68 15.68s7.040 15.68 15.68 15.68 15.68-7.040 15.68-15.68-7.040-15.68-15.68-15.68zM16 29.216c-7.296 0-13.216-5.92-13.216-13.216s5.92-13.216 13.216-13.216 13.216 5.92 13.216 13.216-5.92 13.216-13.216 13.216z"
          ></path>
          <path
            d="M16 32c-8.832 0-16-7.168-16-16s7.168-16 16-16 16 7.168 16 16-7.168 16-16 16zM16 0.672c-8.448 0-15.328 6.88-15.328 15.328s6.88 15.328 15.328 15.328c8.448 0 15.328-6.88 15.328-15.328s-6.88-15.328-15.328-15.328zM16 29.568c-7.488 0-13.568-6.080-13.568-13.568s6.080-13.568 13.568-13.568c7.488 0 13.568 6.080 13.568 13.568s-6.080 13.568-13.568 13.568zM16 3.104c-7.104 0-12.896 5.792-12.896 12.896s5.792 12.896 12.896 12.896c7.104 0 12.896-5.792 12.896-12.896s-5.792-12.896-12.896-12.896z"
          ></path>
        </symbol>
        <symbol id="icon-next" viewBox="0 0 32 32">
          <title>next</title>
          <path
            d="M2.304 18.304h14.688l-4.608 4.576c-0.864 0.864-0.864 2.336 0 3.232 0.864 0.864 2.336 0.864 3.232 0l8.448-8.48c0.864-0.864 0.864-2.336 0-3.232l-8.448-8.448c-0.448-0.448-1.056-0.672-1.632-0.672s-1.184 0.224-1.632 0.672c-0.864 0.864-0.864 2.336 0 3.232l4.64 4.576h-14.688c-1.248 0-2.304 0.992-2.304 2.272s1.024 2.272 2.304 2.272z"
          ></path>
          <path
            d="M29.696 26.752c1.248 0 2.304-1.024 2.304-2.304v-16.928c0-1.248-1.024-2.304-2.304-2.304s-2.304 1.024-2.304 2.304v16.928c0.064 1.28 1.056 2.304 2.304 2.304z"
          ></path>
        </symbol>
        <symbol id="icon-prev" viewBox="0 0 32 32">
          <title>prev</title>
          <path
            d="M29.696 13.696h-14.688l4.576-4.576c0.864-0.864 0.864-2.336 0-3.232-0.864-0.864-2.336-0.864-3.232 0l-8.448 8.48c-0.864 0.864-0.864 2.336 0 3.232l8.448 8.448c0.448 0.448 1.056 0.672 1.632 0.672s1.184-0.224 1.632-0.672c0.864-0.864 0.864-2.336 0-3.232l-4.608-4.576h14.688c1.248 0 2.304-1.024 2.304-2.304s-1.024-2.24-2.304-2.24z"
          ></path>
          <path
            d="M2.304 5.248c-1.248 0-2.304 1.024-2.304 2.304v16.928c0 1.248 1.024 2.304 2.304 2.304s2.304-1.024 2.304-2.304v-16.928c-0.064-1.28-1.056-2.304-2.304-2.304z"
          ></path>
        </symbol>
      </defs>
    </svg>
  </div>
</template>

<script>
import $ from "jquery";
var moment = require("moment");
var momentDurationFormatSetup = require("moment-duration-format");
momentDurationFormatSetup(moment);
const YTPlayer = require("yt-player");
var player;
var timeupdater = null;
export default {
  name: "Player",
  data() {
    return {
      api_key: "AIzaSyD1sOpCAq5hCgsmXd-hYTGRBzw1GZMP1-s",
      circleLeft: null,
      barWidth: null,
      currentTime: null,
      isTimerPlaying: false,
      videotime: 0,
      playing: false,
      show_playlist: false,
      current_video: {
        number: 0,
        video_id: "",
        title: "Title",
        duration: "00:00",
        favorite: false
      },
      url: "",
      playlist: [],
      note: ""
    };
  },
  mounted() {
    player = new YTPlayer("#youtube-player", {
      width: 350,
      height: 200,
      controls: false,
      annotations: false,
      related: false,
      info: false,
      modestBranding: false,
      events: {
        onReady: this.onPlayerReady()
      }
    });
  },
  methods: {
    getVideoId(url) {
      var video_id = url.split("v=")[1];
      var ampersandPosition = video_id.indexOf("&");
      if (ampersandPosition != -1) {
        video_id = video_id.substring(0, ampersandPosition);
      }
      return video_id;
    },
    addInPlaylist() {
      if (this.url !== "") {
        let video = {};
        let video_id = this.getVideoId(this.url);
        $.getJSON(
          `https://www.googleapis.com/youtube/v3/videos?part=snippet,contentDetails&id=${video_id}&key=${this.api_key}`,
          data => {
            video.video_id = video_id;
            video.title = data.items[0].snippet.title;
            video.duration = moment
              .duration(data.items[0].contentDetails.duration)
              .format("hh:mm:ss");
            video.number = this.playlist.length + 1;
            video.favorite = false;
            if (this.playlist.length == 0) {
              this.current_video.favorite = false;
              this.current_video.number = video.number;
              this.current_video.video_id = video_id;
              this.current_video.title = data.items[0].snippet.title;
              this.current_video.duration = moment
                .duration(data.items[0].contentDetails.duration)
                .format("hh:mm:ss");
              this.playlist.push(video);
              this.loadVideo();
              this.playing = true;
            } else {
              this.playlist.push(video);
              this.note = `You are ${video.number} in the list`;
              setTimeout(() => {
                this.note = "";
              }, 2000);
            }
            this.url = "";
          }
        );
      } else {
        this.note = "Please enter a valid link!";
        setTimeout(() => {
          this.note = "";
        }, 2000);
      }
    },
    loadVideo() {
      player.load(this.current_video.video_id, true);
      player.setVolume(100);
      player.on("playing", () => {
        this.isTimerPlaying = true;
      });
      player.on("paused", () => {
        this.isTimerPlaying = false;
      });
      player.on("ended", () => {
        this.nextTrack();
      });
    },
    play() {
      if (this.playlist.length == 0) {
        this.note = "Playlist is empty!";
        setTimeout(() => {
          this.note = "";
        }, 2000);
      } else {
        if (this.isTimerPlaying == false) {
          player.play();
          this.isTimerPlaying = true;
        } else {
          player.pause();
          this.isTimerPlaying = false;
        }
      }
    },
    removeNote() {
      this.note = "";
    },
    togglePlaylist() {
      this.show_playlist = !this.show_playlist;
    },
    generateTime(time) {
      var time_in_seconds = this.getTimeinSeconds(this.current_video.duration);
      let width = (100 / time_in_seconds) * time;
      this.barWidth = width + "%";
      this.circleLeft = width + "%";
      let curmin = Math.floor(time / 60);
      let cursec = Math.floor(time - curmin * 60);
      if (curmin < 10) {
        curmin = "0" + curmin;
      }
      if (cursec < 10) {
        cursec = "0" + cursec;
      }
      this.currentTime = curmin + ":" + cursec;
    },
    updateBar(x) {
      let progress = this.$refs.progress;
      let maxduration = this.getTimeinSeconds(this.current_video.duration);
      let position = x - progress.offsetLeft;
      let percentage = (100 * position) / progress.offsetWidth;
      if (percentage > 100) {
        percentage = 100;
      }
      if (percentage < 0) {
        percentage = 0;
      }
      this.barWidth = percentage + "%";
      this.circleLeft = percentage + "%";
      let jump_to = (maxduration * percentage) / 100;
      player.seek(jump_to);
    },
    clickProgress(e) {
      this.updateBar(e.pageX);
    },
    prevTrack() {
      if (this.playlist.length === 0) {
        this.note = "Playlist is empty!";
        setTimeout(() => {
          this.note = "";
        }, 2000);
      } else {
        var index = this.playlist.findIndex(
          video => video.number === this.current_video.number
        );
        if (index == 0) {
          this.current_video = this.playlist[this.playlist.length - 1];
          this.loadVideo();
        } else {
          this.current_video = this.playlist[index - 1];
          this.loadVideo();
        }
      }
    },
    nextTrack() {
      if (this.playlist.length === 0) {
        this.note = "Playlist is empty!";
        setTimeout(() => {
          this.note = "";
        }, 2000);
      } else {
        var index = this.playlist.findIndex(
          video => video.number === this.current_video.number
        );
        if (index == this.playlist.length - 1) {
          this.current_video = this.playlist[0];
          this.loadVideo();
        } else {
          this.current_video = this.playlist[index + 1];
          this.loadVideo();
        }
      }
    },
    favorite() {
      if (this.playlist.length === 0) {
        this.note = "Playlist is empty!";
        setTimeout(() => {
          this.note = "";
        }, 2000);
      } else {
        this.playlist[this.current_video.number - 1].favorite = !this.playlist[
          this.current_video.number - 1
        ].favorite;
      }
    },
    onPlayerReady() {
      var vm = this;
      function updateTime() {
        var oldTime = vm.videotime;
        if (player && player.getCurrentTime) {
          vm.videotime = player.getCurrentTime();
        }
        if (vm.videotime !== oldTime) {
          vm.generateTime(vm.videotime);
        }
      }
      timeupdater = setInterval(updateTime, 100);
      console.log(timeupdater, "updater");
    },
    getTimeinSeconds(time) {
      return time
        .split(":")
        .reverse()
        .reduce((prev, curr, i) => prev + curr * Math.pow(60, i), 0);
    }
  }
};
</script>
