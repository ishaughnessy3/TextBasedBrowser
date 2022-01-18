from sys import argv, exit
from os import mkdir
from collections import deque
import requests
from bs4 import BeautifulSoup as bs
from colorama import Style, Fore


# write your code here


class TextBasedBrowser:

    def __init__(self, directory):
        self.tabs: dict = {}
        self.headers = {'User-agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) '
                                      'Chrome/81.0.4044.138 Safari/537.36'}
        self.directory: str = directory
        self.cache: deque = deque()
        try:
            mkdir(self.directory)
        except FileExistsError:
            pass

    def cache_tab(self, name: str, content: str) -> None:
        if not self.tabs.get(name[:-4]):
            with open(f'{self.directory}/{name[:-4]}.txt', 'w') as tab:
                tab.write(content)
            self.tabs[name[:-4]] = f'{self.directory}/{name[:-4]}.txt'
            self.cache.append(self.tabs[name[:-4]])

    def open_tab(self, url: str) -> str:
        with open(self.tabs[url], 'r') as tab:
            return tab.read()

    @staticmethod
    def get_tags(content: bytes) -> str:
        tags = ['p', 'a', 'ul', 'ol', 'li', 'h1', 'h2', 'h3', 'h4', 'h5', 'h6']
        soup = bs(content, 'html.parser').body.descendants
        page = ""
        for descendant in soup:
            if descendant.name in tags:
                if descendant.name == 'a':
                    page += Fore.BLUE + descendant.get_text().strip()
                else:
                    page += Style.RESET_ALL + descendant.get_text().strip()
        return page

    def check_url(self, url: str) -> str:
        if url.startswith('http'):
            return self.request(url)
        return self.request('https://' + url)

    def request(self, url: str) -> str:
        request = requests.get(url, params=self.headers)
        clean_page = self.get_tags(request.content)
        self.cache_tab(url[8:], clean_page)
        return clean_page

    def back(self) -> str:
        if len(self.cache) == 1:
            return 'History is Empty'
        try:
            with open(self.cache.popleft(), 'r') as cached:
                return cached.read()
        except FileNotFoundError:
            return 'Error.'

    # Main
    def browser(self) -> None:
        while True:
            url: str = input()
            if '.' in url:
                print(self.check_url(url))
            elif url in self.tabs:
                print(self.open_tab(url))
            elif url == 'back':
                print(self.back())
            elif url == 'exit':
                exit()
            else:
                print('Incorrect URL')


# Argument settings
args = argv
browser = TextBasedBrowser(args[1])
browser.browser()
