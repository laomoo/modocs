# HighCostOneKeyDiagnosis 高额费用一键诊断

## 测试环境

用测环境：[http://134.129.126.194:7010/HighCostOneKeyDiagnosis/HighCostOneKeyDiagnosisService](http://134.129.126.194:7010/HighCostOneKeyDiagnosis/HighCostOneKeyDiagnosisService)

## 接口协议

![](https://ws3.sinaimg.cn/large/005BYqpgly1fxq4w0jq7rj30p60h90u9.jpg)

## 具体报文
### 输入报文
请求报文（24小时内移动流量是否达到5G）：
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ws="http://ws.costdiagnosis.ibs.ffcs.com/">
   <soapenv:Header/>
   <soapenv:Body>
      <ws:execute>
         <!--Optional:-->
         <arg0><![CDATA[
				<ContractRoot>
					<TcpCont>
						<AppKey>5910110002</AppKey>
						<Method>HighCostOneKeyDiagnosis</Method>
						<TransactionID>20180322002035100783</TransactionID>
						<ReqTime>20180316142831</ReqTime>
						<Sign>5050c7f57a665fe0c9dfc44ad2ffaa93</Sign>
						<Version>V1.0</Version>
					</TcpCont>
					<SvcCont>
						<InParam>
						    <dealtype>1</dealtype>
							<latnId>591</latnId>
							<prodInstId>3144978007</prodInstId>
						</InParam>
					</SvcCont>
				</ContractRoot>
				]]></arg0>
      </ws:execute>
   </soapenv:Body>
</soapenv:Envelope>
```
返回输出：
```XML
<S:Envelope xmlns:S="http://schemas.xmlsoap.org/soap/envelope/">
   <S:Body>
      <ns2:executeResponse xmlns:ns2="http://ws.costdiagnosis.ibs.ffcs.com/">
         <return><![CDATA[<?xml version="1.0" encoding="GBK" standalone="yes"?><ContractRoot><TcpCont><AppKey>5910110002</AppKey><Method>HighCostOneKeyDiagnosis</Method><TransactionID>20180322002035100783</TransactionID><ReqTime>20180316142831</ReqTime><Sign>5050c7f57a665fe0c9dfc44ad2ffaa93</Sign><Version>V1.0</Version></TcpCont><SvcCont><OutParam><Result><ResultCode>0</ResultCode><ResultMsg>查询成功</ResultMsg></Result><returnInfo><IfTrue>0</IfTrue><RealValue>68655014</RealValue></returnInfo></OutParam></SvcCont></ContractRoot>]]></return>
      </ns2:executeResponse>
   </S:Body>
</S:Envelope>
```

请求报文（24小时内国际漫游费用是否累计达800元）：
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ws="http://ws.costdiagnosis.ibs.ffcs.com/">
   <soapenv:Header/>
   <soapenv:Body>
      <ws:execute>
         <!--Optional:-->
         <arg0><![CDATA[
				<ContractRoot>
					<TcpCont>
						<AppKey>5910110002</AppKey>
						<Method>HighCostOneKeyDiagnosis</Method>
						<TransactionID>20180322002035100783</TransactionID>
						<ReqTime>20180316142831</ReqTime>
						<Sign>5050c7f57a665fe0c9dfc44ad2ffaa93</Sign>
						<Version>V1.0</Version>
					</TcpCont>
					<SvcCont>
						<InParam>
						    <dealtype>3</dealtype>
							<latnId>591</latnId>
							<prodInstId>58596879</prodInstId>
						</InParam>
					</SvcCont>
				</ContractRoot>
				]]></arg0>
      </ws:execute>
   </soapenv:Body>
</soapenv:Envelope>
```
返回输出：
```XML
<S:Envelope xmlns:S="http://schemas.xmlsoap.org/soap/envelope/">
   <S:Body>
      <ns2:executeResponse xmlns:ns2="http://ws.costdiagnosis.ibs.ffcs.com/">
         <return><![CDATA[<?xml version="1.0" encoding="GBK" standalone="yes"?><ContractRoot><TcpCont><AppKey>5910110002</AppKey><Method>HighCostOneKeyDiagnosis</Method><TransactionID>20180322002035100783</TransactionID><ReqTime>20180316142831</ReqTime><Sign>5050c7f57a665fe0c9dfc44ad2ffaa93</Sign><Version>V1.0</Version></TcpCont><SvcCont><OutParam><Result><ResultCode>0</ResultCode><ResultMsg>查询成功</ResultMsg></Result><returnInfo><IfTrue>0</IfTrue><RealValue>200</RealValue></returnInfo></OutParam></SvcCont></ContractRoot>]]></return>
      </ns2:executeResponse>
   </S:Body>
</S:Envelope>
```