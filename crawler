# -*- coding: utf-8 -*-
"""
@author: Maciej Ulaszewski
mail: ulaszewski.maciej@gmail.com
github: https://github.com/ulaszewskim
"""

import os.path
import pandas as pd
from playsound import playsound
from time import sleep, localtime, strftime, time
from selenium import webdriver, common


def get_num(text):
    '''
    get lastest number on wheel
    returns number 0-54 (int type)
    '''
    num = text[-8:]
    if num[0] == '>':
        num = num[1]
    else:
        num = num[:2]

    return int(num)


def get_queue():
    '''
    create queue dict
    if exists - loads history from data.txt
    if not, starts with '?'
    and starts counting when color appears
    '''
    if not os.path.isfile('data.txt'):
        queue = {'black': '?',
                 'red': '?',
                 'blue': '?',
                 'gold': '?'
        }
    else:
        df = pd.read_csv('data.txt', delimiter=';', header=0)
        if df.shape[0] == 0:
            queue = {'black': '?',
                     'red': '?',
                     'blue': '?',
                     'gold': '?'
            }
        else:
            size = int(df.shape[0]) - 1
        
            last_black = int(df.loc[df['color'] == 'black'].iloc[-1].name)
            last_red = int(df.loc[df['color'] == 'red'].iloc[-1].name)
            last_blue = int(df.loc[df['color'] == 'blue'].iloc[-1].name)
            last_gold = int(df.loc[df['color'] == 'gold'].iloc[-1].name)
        
            last_black = size - last_black
            last_red = size - last_red
            last_blue = size - last_blue
            last_gold = size - last_gold

            queue = {'black': last_black,
                     'red': last_red,
                     'blue': last_blue,
                     'gold': last_gold
                    }

    return queue


def update_queue(queue, color):
    '''
    update value in queue with specified color
    queue - dict from get_queue()
    color - name of color ['black', 'red', 'blue', 'gold']
    '''
    colors = ['black', 'red', 'blue', 'gold']
    for a in colors:
        try:
            queue[a] += 1
        except TypeError:
            pass
    queue[color] = 0

    return queue


def sound_when_color(col, num):
    '''
    plays sound when last roll
    is given color(col)
    col - str, one of colors ('black', 'blue' etc)
    '''
    color = ['gold','blue','black','red','black','red','black','red','black','blue','black','blue','black','red','black','red','black','red','black','blue','black','blue','black','red','black','red','black','red','black','red','black','red','black','blue','black','blue','black','red','black','red','black','red','black','blue','black','blue','black','red','black','red','black','red','black','blue']
    if color[num] == col:
        playsound('get.mp3')


def sound_when_2_colors(col1, col2, n, count, num):
    '''
    plays sound when last n rolls
    are col1 and/or col2
    
    col1, col2 - str, one of colors ('black', 'blue' etc)
    n - int, minimum of rolls with those colors
    count - int, must be definied before start
    of crawling as count = 0
    
    returns count and must be called as:
    count = sound_when_2_colors(col1, col2, n, count)
    '''
    color = ['gold','blue','black','red','black','red','black','red','black','blue','black','blue','black','red','black','red','black','red','black','blue','black','blue','black','red','black','red','black','red','black','red','black','red','black','blue','black','blue','black','red','black','red','black','red','black','blue','black','blue','black','red','black','red','black','red','black','blue']

    if color[num] == col1 or color[num] == col2:
        if count == n - 1:
            playsound('get.mp3')
        else:
            count += 1
    else:
        count = 0

    return count


def main():
    #CALL VARIABLES
    url = 'https://csgo500.com'
    xp = "//section[@id='content-game']//div[100]"
    hsh = None
    queue = get_queue()
    
    #color - list of colors assigned to numbers
    #for example - 0 is gold, 1 is blue etc.
    color = ['gold','blue','black','red','black','red','black','red','black','blue','black','blue','black','red','black','red','black','red','black','blue','black','blue','black','red','black','red','black','red','black','red','black','red','black','blue','black','blue','black','red','black','red','black','red','black','blue','black','blue','black','red','black','red','black','red','black','blue']
    #MAIN FUNCTION
    chromeOptions = webdriver.ChromeOptions()
    chromeOptions.add_argument('headless')
    # chromeOptions.add_argument("--disable-logging")
    chromeOptions.add_argument('log-level=3')
    browser = webdriver.Chrome(chrome_options=chromeOptions)
    browser.get(url)
    count = 0 #for sound_when_2_colors
    while True:
        try:
            elem2 = browser.find_element_by_xpath(xp)
            text = elem2.get_attribute("innerHTML")
        except common.exceptions.StaleElementReferenceException:
            print('ERROR OCCURED')
            browser.close()
            print('Restarting browser...')
            return main()
        if hsh != text[93:157]:
            num = get_num(text)
            hsh = text[93:157]
            with open('log.txt', 'a') as file:
                file.write('{};{};{};{}\n'.format(hsh, int(time()), num, color[num]))
            queue = update_queue(queue, color[num])
            print(strftime("%Y-%m-%d %H:%M:%S  ", localtime()), end='')
            print(str(num) + ' '*(3-len(str(num))), end='')
            print(color[num] + ' '*(7-len(color[num])), end='')
            print('Rolls since last: black: {}, red: {}, blue: {}, gold: {}'.format(queue['black'], queue['red'], queue['blue'], queue['gold']))
            # sound_when_color('gold', num)
            count = sound_when_2_colors('red', 'gold', 3, count, num)
            sleep(5)
        else:
            sleep(5)

main()
