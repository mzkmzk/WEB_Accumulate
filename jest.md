# jest

# 踩坑记录

> jest.mock

jest.mock('jquery')

beforeEach(() => jest.resetModules());

不然 $.ajax.mock.calls情况进行累加 