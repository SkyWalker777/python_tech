clickhouse 案例
##################################################################################

clickhouse Http 端口连接案例
**********************************************************************************

需求描述: 查找 api 峰值及峰值时刻的 code 数据量和成功量。

.. code-block:: python

	import time
	import clickhouse_connect

	client = clickhouse_connect.get_client(host='xxx.xxx.xxx.xxx', port='xxx', username='xxx', password='xxx',
	                                       database='xxxxxx')

	apis = 'aaa,bbb,ccc'.split(',')
	# apis = 'getShopOpenCardInfo'.split(',')

	# api_name = '' # api 名称
	# api_call_count = 0 # 峰值调用量
	# api_timestamp = 0 # 峰值时刻时间戳
	# api_time = '' # 峰值时刻

	for api in apis:
	    # 查询峰值相关信息
	    api_top_info_result = client.command(
	        """select '""" + api + """' as api,sum(call_count) as call_count,timestamp,toString(toDateTime(min(timestamp)), 'Asia/Shanghai') as time from gateway.safe_gateway_risk_monitor_dist where function_id = '""" + api + """' and timestamp >= 1667214000 and timestamp <= 1667998800 group by timestamp as temp order by call_count desc, timestamp asc limit 1;
	    """)

	    api = api_top_info_result[0]
	    api_top_timestamp = api_top_info_result[2]

	    # 查询 code 相关信息
	    api_code_info_result = client.query(
	        """select code, sum(call_count) as sum_count from gateway.gateway_except_stat_dist where function_id = '""" + api + """' and timestamp = """ + api_top_timestamp + """ and (code = '-7' or code = '-9' or code = '-12') group by code""")

	    num_code_7 = 0
	    num_code_9 = 0
	    num_code_12 = 0
	    for api_code_info in api_code_info_result.result_set:
	        if api_code_info[0] == '-7':
	            num_code_7 = api_code_info[1]
	        if api_code_info[0] == '-9':
	            num_code_9 = api_code_info[1]
	        if api_code_info[0] == '-12':
	            num_code_12 = api_code_info[1]

	    api_top_info_result.append(num_code_7)
	    api_top_info_result.append(num_code_9)
	    api_top_info_result.append(num_code_12)

	    # 查询流量出口信息
	    api_flow_monitor_info_result = client.query(
	        """select sum(out_count) from gateway.gateway_flow_monitor_dist where function_id = '""" + api + """' and timestamp = """ +  api_top_timestamp)

	    api_out_count = 0
	    for api_flow_monitor_info in api_flow_monitor_info_result.result_set:
	        api_out_count = api_flow_monitor_info[0]

	    api_top_info_result.append(api_out_count)
	    print(api_top_info_result)

	    time.sleep(1)

Python 使用 clickhouse_driver 操作 clickhouse 数据库
**********************************************************************************

Python要实现使用clickhouse_driver操作clickhouse数据库，首先需要安装clickhouse_driver模块：``pip install clickhouse_driver``
| PIP 安装的时候有时会遇到 WARNING: Retrying (Retry(total=4, connect=None, read=None, redirect=None, status=None)) after connection broken by 'NewConnectionError('<pip._vendor.urllib3.connection.HTTPSConnection object at 0x7fd06b379a90>: Failed to establish a new connection: [Errno -3] Temporary failure in name resolution')': /simple/clickhouse-driver/。如果不是在DOCKER容器中一般可以通过指定PIP源的方法解决，如果是在docker容器中一般要修改docker中的DNS地址

.. code-block:: python

	from clickhouse_driver import Client
	#可以再加个连接超时时间send_receive_timeout=send_receive_timeout
	client = Client(host=host, port=port, user=user, password=password,database=database)
	result = client.execute("select * from 数据库.表 limit 10")
	print(result)

连接的时候可能碰到报错：File "/usr/local/lib/python3.8/site-packages/clickhouse_driver/connection.py", line 437, in receive_hello raise errors.UnexpectedPacketFromServerError(message)
clickhouse_driver.errors.UnexpectedPacketFromServerError: Code: 102. Unexpected packet from server None:None (expected Hello or Exception, got Unknown packet)
这是因为端口使用错误导致，clickhouse有两个端口，8123和9000。分别用于接收 http协议和tcp协议请求，管理后台登录用的8123(jdbc连接)，而程序连接clickhouse(driver连接)则需要使用9000端口。如果在程序中使用8123端口连接就会报错：
clickhouse_driver.errors.UnexpectedPacketFromServerError: Code: 102. Unexpected packet from server None:None (expected Hello or Exception, got Unknown packet)


























































































































