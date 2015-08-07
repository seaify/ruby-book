# rails 开发原则
1. 生成controller中函数时，坚定使用 rails generate controller Say hello goodbye这种方法，不要直接在controller里直接去写函数，因为不会生成对应的测试文件和view文件
2. 必须建立测试，而不能依靠外部对接接口来替你做测试
3. 需要测试先行，先写测试，后写开发代码
4. 必须依赖guard, 来完成一些重复任务，如gemfile中添加gem后，自动安装，生成db migration文件后自动执行db:migrate, db/schema.rb变更后，自动annotate等