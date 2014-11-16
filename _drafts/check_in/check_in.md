    class User < ActiveRecord::Base

      #  存放用户累计签到天数
      # {"user_id" => "checkin_count"}
      # {"232213"=>"10","3331223"=>"21","3331224"=>"13"}
      hash_key :checkin_days, global: true


      # 一个约 30万的 big redis set，存放今日签到的用户 id
      # key 基于日期生成
      # [3123412, 32312332, 15242545]
      set :checkin_users_today,
        key: proc { |user| "u:checkin_users:#{ Date.today }" },
        expiration: 7.day,
        global: true

      # 一个约 30万的 big redis set，存放的数据为昨日签到的用户 id
      # key 基于日期生成
      # 只读不写
      # [3123412, 32312332, 15242545]
      set :checkin_users_yesterday,
        key: proc { |user| "u:checkin_users:#{ Date.yesterday }" },
        expiration: 7.day,
        global: true
    end


