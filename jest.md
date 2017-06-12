# jest

# 踩坑记录

> jest.mock

jest.mock('jquery')

beforeEach(() => jest.resetModules());

不然 $.ajax.mock.calls情况进行累加 

mock jqeury 单一

https://gist.github.com/jcouyang/34686f695cd28309759e

react和jest: http://www.bijishequ.com/detail/379328?p=14