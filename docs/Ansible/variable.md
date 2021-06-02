# 変数

## マジック変数

[Special Variables — Ansible Documentation](https://docs.ansible.com/ansible/latest/reference_appendices/special_variables.html)

### omit

省略したいパラメタにセットすると無視されるようになる。  
[[Ansible] 省略したいパラメタに変数指定せざるを得ない場合に使う変数omitとdefaultフィルタ - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2021/06/02/090251)

```yaml
  - name: create files by csv
    ansible.builtin.file:
      state: "{{ item.type }}"
      path: "{{ item.path }}"
      mode: "{{ item.mode | default(omit, true) }}"
      owner: "{{ item.owner | default(omit, true) }}"
      group: "{{ item.group | default(omit, true) }}"
    loop: "{{ res_input.list }}"
```
