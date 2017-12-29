/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

 \* 首页

 \*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

import {

    alertMsg,

    confirmMsg,

    doApplyCondition,

    doApplyConditionStr,

    findPageDom,

    findPageView,

    formatSeconds,

    getDeviceId,

    getToken,

    isEmpty,

    loadNativePage,

    loadPage,

    momentMDStr,

    momentYMDHM,

    onLogout,

    removeAuthZhima,

    removeItem,

    renderPageHTMLByTplId,

    returnToIndex,

    saveItem

} from "../utils/appHelper";

import xhr from "../utils/xhr";

export default  {

    initView\(\){

        let LOAN\_AMOUNT;

        this.init\(\);

        this.pullToRefresh\(\);

        this.bindEvents\(\);

    },

    init\(\){

        this.isUserLogined\(\)

            .then\(\(loginData\) =&gt; {

                if \(isEmpty\(loginData\)\) {

                    //this.LeftBarbinds\(\);

                    return Promise.resolve\(loginData\)

                } else {

                    //主页显示姓名

                    findPageDom\('mainView', 'userName'\).text\(loginData.userName\);

                    //this.LeftBarbinds\(true, loginData\);

                    //获取上一次申请信息

                    return this.getLastApplyInfo\(\);

                }

            }\)

            .then\(\(data\) =&gt; {

                if \(isEmpty\(data\)\) {

                    //没有上一次申请信息

                    this.commonIndex\(true\);

                }

                let newData = Object.assign\({}, data\);

                return Promise.resolve\(newData\)

            }\)

            .then\(\(data\) =&gt; {

                if \(Object.keys\(data\).length === 0\) {

                    return data;

                } else {

                    //检查 是否点击过我知道了

                    return this.checkIknow\(data\);

                }

            }\)

            .then\(\(data\) =&gt; {

                if \(Object.keys\(data\).length !== 0\) {

                    if \(!data.isCheckIknow\) {

                        // 点击过我知道了 判断是回到首页 还是还款页面

                        if \(data.applyStatus === 3\) {

                            //正常首页

                            this.commonIndex\(false\);

                            this.indexCutDownTime\(data.id, data.accountId\);

                        }

                        if \(data.applyStatus === 6\) {

                            //还款页面

                            this.repaymentIndex\(\);

                        }

                    } else {

                        //没有点击过我知道了 继续判断

                        if \(isEmpty\(data\)\) {

                            //没有请求过订单 显示首页

                            this.commonIndex\(true\);

                        } else {

                            //有在发起的订单 显示进度页面

                            this.processIndex\(data\);

                        }

                    }

                }

            }\)

    },

    //检测登录

    isUserLogined\(\){

        let token = getToken\(\);

        let getLoginData = new Promise\(\(resolve, reject\) =&gt; {

            xhr.ajaxRequest\({

                url: 'apply/checkIsLogin',

                method: 'POST',

                data: {

                    'accessToken': token

                },

                success: \(data\) =&gt; {

                    resolve\(data\)

                }

            }\)

        }\);

        return getLoginData

    },

    //获取最后一次申请单信息 区分入口

    getLastApplyInfo\(\){

        let getLastApplyInfo = new Promise\(\(resolve, reject\) =&gt; {

            xhr.ajaxRequest\({

                url: 'apply/getLastApplyInfo',

                method: 'POST',

                success: \(data\) =&gt; {

                    resolve\(data\);

                }

            }\)

        }\);

        return getLastApplyInfo;

    },

    //检查 是否点击过我知道了

    checkIknow\(data\){

        let checkIknow = new Promise\(\(resolve, reject\) =&gt; {

            xhr.ajaxRequest\({

                url: 'apply/checkIKnow',

                method: 'POST',

                data: {

                    applyId: data.id

                },

                success: \(d\) =&gt; {

                    let newData = Object.assign\({}, data, {

                        isCheckIknow: d

                    }\);

                    resolve\(newData\);

                }

            }\)

        }\);

        return checkIknow;

    },

    //正常首页

    commonIndex\(isShowBtn\){

        $$\('\#borrowMainIndex'\).show\(\);

        this.getProductData\(isShowBtn\);

        this.rangerSlider\(\);

        this.messageSlide\(\);

    },

    //进度页面

    processIndex\(data\){

        $$\('\#loanProcessIndex'\).show\(\);

        this.getApplyFollowList\(data.id, data.loanPrincipal, data.applyStatus, data.accountId\);

        if \(data.applyStatus !== 3 && data.applyStatus !== 6 && data.applyStatus !== 5\) {

            if \(this.interval\) {

                clearInterval\(this.interval\)

            }

            this.interval = setInterval\(\(\) =&gt; {

                this.getApplyFollowList\(data.id, data.loanPrincipal, data.applyStatus, data.accountId, this.interval\)

            }, 1000 \* 30\);

        }

    },

    //还款页面

    repaymentIndex\(\){

        this.getLastOrderInfo\(\);

    },

    //首页滑动条

    rangerSlider\(\){

        var width = $$\('.rangerWrapper'\).width\(\);

        $\('.slider-input'\).jRange\({

            from: 500,

            to: 1000,

            step: 100,

            format: '%s',

            width: width,

            showLabels: true,

            ondragend: function \(value\) {

                alertMsg\('保持良好的信用有助于提升额度', \(\) =&gt; {

                    $\('.slider-input'\).jRange\('setValue', 500\);

                }\);

            }

        }\);

    },

    //立即借款

    loadAuthPage\(productId, isShowBtn\) {

        if \(!isShowBtn\) {

            findPageDom\('mainView', 'clickToBorrowHook'\).hide\(\);

        } else {

            findPageDom\('mainView', 'clickToBorrowHook'\).removeClass\('cutDown'\).text\('立即借款'\).show\(\).off\('click'\).on\('click', \(\) =&gt; {

                this.isUserLogined\(\)

                    .then\(\(loginData\) =&gt;{

                        if\(isEmpty\(loginData\)\){

                            loadNativePage\('login'\);

                        }else{

                            let queryData = {

                                'id': productId

                            };

                            removeItem\('isShow'\);

                            removeAuthZhima\(\);

                            loadPage\('doAuthConfirm.html', queryData\);

                        }

                    }\);

                //没有token与deviceId,直接去登录页面

               /\* console.log\('TOKEN:' + getToken\(\)\);

                console.log\('DeviceId:' + getDeviceId\(\)\);

                if \(!getToken\(\) \|\| !getDeviceId\(\)\) {

                    loadNativePage\('login'\);

                } else {

                    let queryData = {

                        'id': productId

                    };

                    removeItem\('isShow'\);

                    removeAuthZhima\(\);

                    loadPage\('doAuthConfirm.html', queryData\);

                }\*/

            }\);

        }

    },

    //获取产品列表

    getProductData\(isShowBtn\){

        xhr.ajaxRequest\({

            url: 'apply/getProductList',

            method: 'GET',

            success: \(data\) =&gt; {

                //因为上线只有一款产品 就先这么处理

                this.LOAN\_AMOUNT = 1000;

                findPageDom\('mainView', 'datedAmountInfo'\).text\(data\[0\].interestAmount + data\[0\].loanAmount\);

                findPageDom\('mainView', 'canBorrowAmount'\).text\(this.LOAN\_AMOUNT\);

                $\('.slider-input'\).jRange\('setValue', data\[0\].loanAmount\);

                let id = data\[0\].id;

                this.loadAuthPage\(id, isShowBtn\);

            }

        }\)

    },

    //消息通知

    messageSlide\(\){

        xhr.ajaxRequest\({

            url: 'apply/getIndexMessageInfo',

            method: 'POST',

            data: {

                code: 1  //1 代表正常首页

            },

            success: \(data\) =&gt; {

                if \(data\) {

                    if \(!isEmpty\(data.message\)\) {

                        renderPageHTMLByTplId\('mainView', 'messageSlideTemplate', 'messageSlideTemplate', data.message\);

                        let $$messageSlide = findPageDom\('mainView', 'messageSlideTemplate'\);

                        setTimeout\(\(\) =&gt; {

                            $$messageSlide.show\(\).addClass\('fadeInDown'\);

                        }, 1000\);

                        findPageDom\('mainView', 'closeSlideMessage'\).off\('click'\).on\('click', \(\) =&gt; {

                            $$messageSlide.removeClass\('fadeInDown'\).addClass\('fadeOutUp'\).hide\(\).removeClass\('fadeOutUp'\);

                        }\)

                    }

                }

            }

        }\)

    },

    bindEvents\(\){

        //左侧栏

        $$\('.userInfo'\).off\('click'\).on\('click', \(\) =&gt; {

            this.isUserLogined\(\)

                .then\(\(data\) =&gt; {

                    if \(isEmpty\(data\)\) {

                        loadNativePage\('login'\);

                    } else {

                        //侧边栏展开状态效果调整

                        /\*$$\('.panel-left'\).on\('open', function \(\) {

                            $$\(this\).addClass\('open'\);

                        }\);

                        $$\('.panel-left'\).on\('opened', function \(\) {

                            $$\(this\).removeClass\('open'\);

                        }\);\*/

                        //帮助中心

                        $$\('.helpCenter'\).off\('click'\).on\('click', \(\) =&gt; {

                            loadPage\('helpInfoList.html'\);

                            myApp.closePanel\(\);

                        }\);

                        //借款记录

                        $$\('.borrowRecord'\).off\('click'\).on\('click', \(\) =&gt; {

                            loadPage\('borrowRecord.html'\);

                            myApp.closePanel\(\);

                        }\);

                        //意见反馈

                        $$\('.feedBack'\).off\('click'\).on\('click', \(\) =&gt; {

                            loadPage\('advice.html'\);

                            myApp.closePanel\(\);

                        }\);

                        $$\('.userTel'\).text\(data.mobileNum\);

                        //主页显示姓名

                        findPageDom\('mainView', 'userName'\).text\(data.userName\);

                        //登出

                        $$\('.logOutBtn'\).off\('click'\).on\('click', \(\) =&gt; {

                            confirmMsg\('确认退出吗？', \(\) =&gt; {

                                onLogout\(\);

                                alertMsg\('退出成功', \(\) =&gt; {

                                    returnToIndex\(\);

                                }\);

                                // myApp.closePanel\(\);

                                // this.initView\(\);

                            }\)

                        }\);

                        //打开left panel

                        myApp.openPanel\('left'\);

                    }

                }\)

        }\);

        var mainView=findPageView\('mainView'\);

        if\(mainView\){

            //认证

            mainView.find\('.authInfo'\).off\('click'\).on\('click', \(\) =&gt; {

                this.isUserLogined\(\)

                    .then\(\(loginData\) =&gt;{

                        if\(isEmpty\(loginData\)\){

                            loadNativePage\('login'\);

                        }else{

                            saveItem\('isShow', 'false'\);

                            loadPage\('doAuthConfirm.html'\)

                        }

                    }\);

            }\);

            //消息

            $$\('.messageMore'\).off\('click'\).on\('click', \(\) =&gt; {

                this.isUserLogined\(\)

                    .then\(\(loginData\) =&gt;{

                        if\(isEmpty\(loginData\)\){

                            loadNativePage\('login'\);

                        }else{

                            loadPage\('messagePage.html'\);

                        }

                    }\);

            }\);

        }

    },

    //打开侧边栏 显示姓名

    LeftBarbinds\(islogin, data\){

        if \(islogin\) {

            $$\('.userInfo'\).addClass\('open-panel'\);

            //侧边栏展开状态效果调整

            $$\('.panel-left'\).on\('open', function \(\) {

                $$\(this\).addClass\('open'\);

            }\);

            $$\('.panel-left'\).on\('opened', function \(\) {

                $$\(this\).removeClass\('open'\);

            }\);

            //帮助中心

            $$\('.helpCenter'\).off\('click'\).on\('click', \(\) =&gt; {

                loadPage\('helpInfoList.html'\);

                myApp.closePanel\(\);

            }\);

            //借款记录

            $$\('.borrowRecord'\).off\('click'\).on\('click', \(\) =&gt; {

                loadPage\('borrowRecord.html'\);

                myApp.closePanel\(\);

            }\);

            //意见反馈

            $$\('.feedBack'\).off\('click'\).on\('click', \(\) =&gt; {

                loadPage\('advice.html'\);

                myApp.closePanel\(\);

            }\);

            $$\('.userTel'\).text\(data.mobileNum\);

            //主页显示姓名

            findPageDom\('mainView', 'userName'\).text\(data.userName\);

            //登出

            $$\('.logOutBtn'\).off\('click'\).on\('click', \(\) =&gt; {

                confirmMsg\('确认退出吗？', \(\) =&gt; {

                    onLogout\(\);

                    alertMsg\('退出成功', \(\) =&gt; {

                        returnToIndex\(\);

                    }\);

                    // myApp.closePanel\(\);

                    // this.initView\(\);

                }\)

            }\);

        } else {

            $$\('.userInfo'\).off\('click'\).on\('click', \(\) =&gt; {

                loadNativePage\('login'\);

            }\).removeClass\('open-panel'\);

        }

        //认证资料

        findPageDom\('mainView', 'authInfo'\).off\('click'\).on\('click', \(\) =&gt; {

            if \(!getToken\(\) \|\| !getDeviceId\(\)\) {

                loadNativePage\('login'\);

            } else {

                saveItem\('isShow', 'false'\);

                loadPage\('doAuthConfirm.html'\)

            }

        }\);

        //消息页面

        $$\('.messageMore'\).off\('click'\).on\('click', \(\) =&gt; {

            if \(!getToken\(\) \|\| !getDeviceId\(\)\) {

                loadNativePage\('login'\);

            } else {

                loadPage\('messagePage.html'\);

            }

        }\);

    },

    //获取流水详情

    getApplyFollowList\(id, amount, applyStatus, accountId, interval\){

        findPageDom\('mainView', 'canBorrowAmount'\).text\(amount\);

        xhr.ajaxRequest\({

            url: 'apply/getApplyFlowListByApplyId',

            method: 'POST',

            data: {

                applyId: id

            },

            success: \(data\) =&gt; {

                if \(interval\) {

                    if \(data\[0\].applyStatus === 3 \|\| data\[0\].applyStatus === 6 \|\| data\[0\].applyStatus === 5\) {

                        clearInterval\(interval\)

                    }

                }

                xhr.ajaxRequest\({

                    url: 'apply/getBankCardInfo',

                    method: 'POST',

                    data: {

                        isShow: 0

                    },

                    success: \(userBankInfo\) =&gt; {

                        //银行卡信息

                        let userBankInfoStr;

                        let str = '借款已成功打到您 ' + userBankInfo.cardBank + '尾号' + userBankInfo.cardNo.substr\(userBankInfo.cardNo.length - 4\) + ' 的卡上';

                        userBankInfoStr = str;

                        //处理模版数据

                        $$.each\(data, \(i, e\) =&gt; {

                            if \(e.applyStatus === 6\) {

                                e.applyStatusInfo = userBankInfoStr

                            } else {

                                e.applyStatusInfo = doApplyConditionStr\(e.applyStatus\);

                            }

                            e.applyStatusStr = doApplyCondition\(e.applyStatus\);

                            e.time = momentYMDHM\(e.gmtCreate\);

                        }\)

                        renderPageHTMLByTplId\('mainView', 'processDetailTemplate', 'processDetailTemplate', data\);

                        this.doApplyFollowList\(data\[0\].applyStatus, data.length, id, applyStatus, accountId\);

                    }

                }\)

            }

        }\)

    },

    //处理流水单详情

    doApplyFollowList\(pram, length, id, applyStatus, accountId\){

        //根据状态显示不同状态的流水详情

        if \(pram === 3 \|\| pram === 5\) {  //3审核未通过  5放款失败

            findPageDom\('mainView', 'currentCommonCondition'\).eq\(0\).addClass\('red'\);

            findPageDom\('mainView', 'circle'\).eq\(0\).addClass\('red'\);

        } else {

            findPageDom\('mainView', 'currentCommonCondition'\).eq\(0\).addClass\('green'\);

            findPageDom\('mainView', 'circle'\).eq\(0\).addClass\('green'\);

        }

        if \(pram === 3 \|\| pram === 6\) { //3审核未通过 6放款成功

            //显示【我知道了】

            this.clickIknow\(id, applyStatus, accountId\);

        }

        if \(length &gt; 1\) {

            //处理流水详情的line ui效果

            let p1 = findPageDom\('mainView', 'currentCommonCondition'\)\[0\].getBoundingClientRect\(\).top;

            let p2 = findPageDom\('mainView', 'currentCommonCondition'\)\[length - 1\].getBoundingClientRect\(\).top;

            let lineHeight = p2 - p1;

            findPageDom\('mainView', 'line'\).css\('height', lineHeight + 'px'\);

        }

    },

    //点击我知道了 发送点击标记

    clickIknow\(id, accountId\){

        findPageDom\('mainView', 'backToIndex'\).show\(\).off\('click'\).on\('click', \(\) =&gt; {

            xhr.ajaxRequest\({

                url: 'apply/clickIKnow',

                method: 'POST',

                data: {

                    applyId: id,

                    accountId: accountId

                },

                success: \(\(\) =&gt; {

                    returnToIndex\(\)

                }\)

            }\)

        }\)

    },

    //获取最后一次订单状态

    getLastOrderInfo\(\){

        xhr.ajaxRequest\({

            url: 'apply/getLastOrderInfo',

            method: 'POST',

            success: \(data\) =&gt; {

                //还清状态

                if \(data.orderStatus === 2 \|\| data.orderStatus === 3\) {

                    this.commonIndex\(true\);

                } else {

                    //显示应还详情

                    $$\('\#repaymentIndex'\).show\(\);

                    data.repaymentDeadlineDateStr = momentMDStr\(data.repaymentDeadlineDate\);

                    renderPageHTMLByTplId\('mainView', 'repaymentIndexTemplate', 'repaymentIndexTemplate', data\);

                    //显示提示

                    this.repaymentMessage\(\);

                    //点击立即还款

                    this.loadToRepayment\(data.id \|\| ''\);

                }

            }

        }\)

    },

    //还款页面上方提示

    repaymentMessage\(\){

        let $$repaymentMessageHook = findPageDom\('mainView', 'repaymentMessageHook'\);

        $$repaymentMessageHook.addClass\('fadeInDown'\);

        findPageDom\('mainView', 'closeSlideMessage'\).off\('click'\).on\('click', \(\) =&gt; {

            $$repaymentMessageHook.hide\(\);

        }\)

    },

    //立即还款

    loadToRepayment\(id\){

        findPageDom\('mainView', 'clickToRepayment'\).off\('click'\).on\('click', \(\) =&gt; {

            // alertMsg\('您的借款暂未到期，请您在还款日再来还款。'\)

            loadPage\('repayment.html', {

                orderId: id

            }\);

        }\)

    },

    //判断倒计时

    indexCutDownTime\(id, accountId\){

        xhr.ajaxRequest\({

            url: 'apply/clickIKnow',

            method: 'POST',

            data: {

                applyId: id,

                accountId: accountId

            },

            success: \(\(data\) =&gt; {

                let interval;

                if \(!isEmpty\(data\)\) {

                    let lineTime = data.gmtCreate + data.refuseDays \* 24 \* 3600 \* 1000;

                    let timestamp = Date.parse\(new Date\(\)\);

                    interval = \(lineTime - timestamp\) / 1000;

                    if \(interval &gt;= 0\) {

                        findPageDom\('mainView', 'clickToBorrowHook'\).off\('click'\).text\('锁定期： ' + formatSeconds\(interval\) + ' 后可重新申请'\).addClass\('cutDown'\).show\(\);

                        this.timer = setInterval\(\(\) =&gt; {

                            interval -= 1;

                            let intervalTimeStr = '锁定期： ' + formatSeconds\(interval\) + ' 后可重新申请';

                            findPageDom\('mainView', 'clickToBorrowHook'\).text\(intervalTimeStr\);

                            if \(interval &lt; 0\) {

                                clearInterval\(this.timer\);

                                returnToIndex\(\);

                            }

                        }, 1000\);

                    } else {

                        this.commonIndex\(true\);

                    }

                }

            }\)

        }\)

    },

    //下拉刷新

    pullToRefresh\(\){

        findPageDom\('mainView', 'pull-to-refresh-content'\).off\('refresh'\).on\('refresh', \(\) =&gt; {

            if \(this.timer\) {

                clearInterval\(this.timer\)

            }

            this.init\(\);

            myApp.pullToRefreshDone\(\);

        }\);

    }

}



