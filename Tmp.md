查询洗车员每日的销售额

``` sql

SELECT
    sl_worker_daily.date,
    sl_worker_daily.worker_id,
    IFNULL(charge.sell_done, 0) - IF(sl_worker_daily.city_id IN(8, 7, 11, 10, 13), 0, IFNULL(refund.refund_success_amount, 0)) as sell_done,
    sl_worker_daily.city_id
FROM sl_worker_daily
LEFT JOIN (
    SELECT
        worker_id as worker_id,
        round(sum(needpay_amount), 2) as sell_done
    FROM sl_userCharge
    WHERE charge_confirm = 1 AND r_user_id > 0 AND (refund_status != 20 OR refund_status IS NULL)
    AND confirm_time between '2016-05-30' AND '2016-05-30 23:59:59'
    GROUP BY worker_id
) charge ON sl_worker_daily.worker_id=charge.worker_id
LEFT JOIN (
    SELECT
        worker_id,
        round(sum(ifnull(refund_amount, 0)), 2) as refund_success_amount
    FROM sl_userCharge
    WHERE r_user_id > 0 AND refund_status = 20
    AND refund_success_time between '2016-05-30' AND '2016-05-30 23:59:59'
    GROUP BY worker_id
) refund ON sl_worker_daily.worker_id=refund.worker_id
WHERE sl_worker_daily.date='2016-05-30' -- AND charge.sell_done > 0
ORDER BY sl_worker_daily.worker_id;

```

查询站在每日的销售额

``` sql
SELECT sl_leader_daily.region_names, worker_daily.sell_done, sl_leader_daily.sell_done
FROM sl_leader_daily
LEFT JOIN (
    SELECT
        SUM(sl_worker_daily.sell_done) as sell_done,
        sl_region_daily.r_admin_id as r_admin_id
    FROM sl_worker_daily
    LEFT JOIN sl_region_daily
    ON sl_worker_daily.region_code = sl_region_daily.region_code AND sl_region_daily.date = '2016-05-30'
    WHERE sl_worker_daily.date = '2016-05-30'
    GROUP BY sl_worker_daily.r_admin_id
) worker_daily ON sl_leader_daily.r_admin_id = worker_daily.r_admin_id
WHERE sl_leader_daily.date = '2016-05-30'
ORDER BY worker_daily.sell_done;

--- 简单一点的
SELECT sl_leader_daily.region_names, SUM(sl_worker_daily.sell_done) as sell_done, sl_leader_daily.sell_done
FROM sl_leader_daily
LEFT JOIN sl_worker_daily
ON sl_leader_daily.r_admin_id = sl_worker_daily.r_admin_id AND sl_worker_daily.date = '2016-05-30'
WHERE sl_leader_daily.date = '2016-05-30'
GROUP BY sl_leader_daily.r_admin_id
ORDER BY sell_done;

```

查询督导每日的销售额

``` sql

SELECT sl_master_daily.r_admin_id, SUM(IFNULL(sl_leader_daily.sell_done, 0)) as sell_done, sl_master_daily.sell_done
FROM sl_master_daily
LEFT JOIN sl_leader_daily
ON sl_master_daily.r_admin_id = sl_leader_daily.r_master_id AND sl_leader_daily.date = '2016-05-30'
WHERE sl_master_daily.date = '2016-05-30'
GROUP BY sl_master_daily.r_admin_id
ORDER BY sell_done;

```
MRHZAWQDULVET6JC, CGZKA7BJ5YM2HN9L, L86EQSD2XNW754FA, ZXYVL36HP2NSK7U, .ZXYVL36HP2NSK7UD,AS9E7XGH2YC4L8TZ,PS6WFE3XKBHRTCNG,E5AL7WQDZHP8NT4R,7QNJKSZ5REDFYV83
from slWashCar.bussiness.BCMPlate import encrypt, cm_base_request, CM_SP_ID
from slWashCar.dmodel.DCouponCode import M_CouponCode

couponcode = M_CouponCode.objects.filter(code='7QNJKSZ5REDFYV83')
couponcode = couponcode.first()
usage = u'用户呱呱兑换洗车券'
code = encrypt(couponcode.code)
use_time = couponcode.exchange_time
content = {'sp_id': CM_SP_ID, 'code': code, 'use_time': use_time.strftime('%Y-%m-%d %H:%M:%S'), 'usage': usage}
cm_base_request('use_coupon', content)


