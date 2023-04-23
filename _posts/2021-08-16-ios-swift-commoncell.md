---
title: "一种通用 TableViewCell - Swift"
layout: post
date: 2021-9-8 13:00
image: #/assets/images/markdown.jpg
headerImage: false
tag:
- iOS
- Swfit
category: blog
author: #Yuan Lee
description: #Markdown summary with different options
# jemoji: '<img class="emoji" title=":ramen:" alt=":ramen:" src="http://localhost:4000/assets/images/profile.jpg" height="20" width="20" align="absmiddle">'
---

## Summary

一种通用的 TableViewCell 样式。包含通用的 Cell 模型和 UI 定义。

---

### 1. CommonCellModel
- 通用 Cell 对应的通用模型
{% highlight raw %}
//
//  CommonCellModel.swift
//
//  Created by Yuan Lee on 2021/9/8.
//

import UIKit

/// CommonCell 的样式
enum CommonCellStyle: Int {
    /// 通用样式
    case common = 0
    /// 右侧显示详情图片
    case detailImage = 1
    /// 标题加红色星号 *
    case titleRedStar = 2
    /// 作为 Seciton 的 Title
    case sectionTitle = 3
    /// 右侧显示控制开关
    case switchControl = 4
}

/// CommonCellModel 模型
class CommonCellModel: NSObject {

    var style = CommonCellStyle.common
    
    /// 标题文字
    @objc var title = String()
    
    /// 详情文字，默认为空字符串
    @objc var detail = String()
    
    /// 图标名称，默认为空字符串
    @objc var iconImageName = String()
    
    /// 是否显示红点，默认不显示
    @objc var showRedPoint = false
    
    /// 开关属性，默认为 true
    @objc var switchOn = true
    
    /// 右侧图标名称，默认为空字符串
    @objc var detailImageName = String()
    
    /// 右侧图标名称，默认为空字符串
    @objc var detailImageSize = CGSize.zero
    
    /// 是否显示右侧箭头，默认显示
    @objc var showRightArrow = true
    
    /// 标题字体
    @objc var titleFont = SYSTEMFONT(14)
    
    /// 标题颜色
    @objc var titleColor = kDarkTextColor
    
    /// 详情字体
    @objc var detailFont = SYSTEMFONT(14)
    
    /// 详情颜色
    @objc var detailColor = kLightTextColor
    
    override init() {
        super.init()
    }
    
    /// CommonCellModel 初始化 - For Swift
    /// - Parameters:
    ///   - title:           标题文字
    ///   - detail:          详情文字，默认为空字符串
    ///   - iconImageName:   图标名称，默认为空字符串
    ///   - showRedPoint:    是否显示小红点，默认不显示
    ///   - showRightArrow:  是否显示右侧箭头，默认显示
    init(title: String, detail: String = String(), iconImageName: String = String(), showRedPoint: Bool = false, showRightArrow: Bool = true) {
        
        super.init()
        
        self.style           = .common
        self.title           = title
        self.detail          = detail
        self.showRedPoint    = showRedPoint
        self.iconImageName   = iconImageName
        self.showRightArrow  = showRightArrow
    }
    
    /// CommonCellModel 初始化 - 右侧详情显示图片 - For Swift
    /// - Parameters:
    ///   - title:           标题文字
    ///   - iconImageName:   图标名称，默认为空字符串
    ///   - detailImageName: 右侧详情图片，默认为空，可以为本地图片名称，也可以为图片链接
    ///   - detailImageSize: 右侧详情图片大小，默认为 zero
    ///   - showRightArrow:  是否显示右侧箭头，默认显示
    init(title: String, iconImageName: String = String(), detailImageName: String, detailImageSize: CGSize, showRightArrow: Bool = true) {
        
        super.init()
        
        self.style           = .detailImage
        self.title           = title
        self.iconImageName   = iconImageName
        self.showRightArrow  = showRightArrow
        self.detailImageName = detailImageName
        self.detailImageSize = detailImageSize
    }
    
    /// CommonCellModel 初始化 - 右侧显示开关 - For Swift
    /// - Parameters:
    ///   - title: 标题文字
    ///   - switchOn: 开关属性，默认为 true
    init(title: String, switchOn: Bool) {
        
        super.init()
        
        self.style           = .switchControl
        self.title           = title
        self.switchOn        = switchOn
        self.showRightArrow  = false
    }
    
    /// CommonCellModel 初始化 - For OC
    /// - Parameters:
    ///   - title:          标题文字
    ///   - detail:         详情文字
    ///   - iconImageName:      图标名称
    ///   - haveRedPoint:   是否显示小红点
    ///   - haveRightArrow: 是否显示右侧箭头
    @objc init(title: String, detail: String, iconImageName: String, haveRedPoint: Bool, haveRightArrow: Bool) {
        super.init()
        self.title          = title
        self.detail         = detail
        self.showRedPoint   = haveRedPoint
        self.iconImageName  = iconImageName
        self.showRightArrow = haveRightArrow
    }
    
    /// 设置 Cell 样式及字体
    /// - Parameters:
    ///   - style: Cell 样式
    ///   - titleFont: 标题字体
    ///   - detailFont: 详情字体
    internal func setStyle(_ style: CommonCellStyle = .common, titleFont: UIFont = SYSTEMFONT(14), detailFont: UIFont = SYSTEMFONT(14), titleColor: UIColor = kDarkTextColor, detailColor: UIColor = kLightTextColor) {
        self.style = style
        self.titleFont = titleFont
        self.detailFont = detailFont
        self.titleColor = titleColor
        self.detailColor = detailColor
    }

}
{% endhighlight %}

### 2.CommonTableViewCell
- 通用 CommonTableViewCell 定义
{% highlight raw %}
//
//  CommonTableViewCell.swift
//
//  Created by Yuan Lee on 2021/9/8.
//

import UIKit
import SnapKit

/// Int 作为返回值的回调闭包
typealias IntCallBackBlock = (_ index: Int) -> (Void)

/// CommonTableViewCell 可重用标识符
let CommonTableViewCellID = "CommonTableViewCellID"
/// 左侧边缘间距
let LeftMargin: CGFloat = 10
/// 右侧边缘距离
let RightMargin: CGFloat = 10
/// 内部之间的间距
let InsideMargin: CGFloat = 5
/// 标签的高度
let LabelHeight: CGFloat = 22
/// 右侧箭头的宽度
let ArrowViewWidth: CGFloat = 6
/// 右侧箭头的高度
let ArrowViewHeight: CGFloat = 12

/// 通用的 Cell 样式
class CommonTableViewCell: UITableViewCell {

    /// CommonCellModel 模型
    @objc var cellModel: CommonCellModel? {
        
        // 设置模型
        didSet {
            
            titleLabel.text = cellModel?.title
            detailLabel.text = cellModel?.detail
            
            // 设置一些样式
            if titleLabel.font != cellModel?.titleFont {
                titleLabel.font = cellModel?.titleFont
            }
            if detailLabel.font != cellModel?.detailFont {
                detailLabel.font = cellModel?.detailFont
            }
            if titleLabel.textColor != cellModel?.titleColor {
                titleLabel.textColor = cellModel?.titleColor
            }
            if detailLabel.textColor != cellModel?.detailColor {
                detailLabel.textColor = cellModel?.detailColor
            }
            
            if cellModel?.style == .sectionTitle
                || cellModel?.style == .switchControl {
                // 标题 或 开关 Cell，无选中效果
                selectionStyle = .none
            } else if cellModel?.style == .titleRedStar {
                addRedStarToTitle()
            }
            
            if cellModel?.showRightArrow == false {
                arrowImageView.snp.updateConstraints { make in
                    make.width.equalTo(0)
                    make.right.equalToSuperview().offset(InsideMargin-RightMargin)
                }
            } else {
                arrowImageView.snp.updateConstraints { make in
                    make.width.equalTo(ArrowViewWidth)
                    make.right.equalToSuperview().offset(-RightMargin)
                }
            }
            
            if cellModel?.detailImageName.count ?? 0 == 0 {
                rightImageView.snp.updateConstraints { make in
                    make.width.equalTo(0)
                    make.right.equalTo(arrowImageView.snp.left)
                }
            } else {
                if cellModel!.detailImageName.hasPrefix("http") {
                    rightImageView.sd_setImage(with: URL.init(string: cellModel!.detailImageName), placeholderImage: nil, options: .retryFailed)
                } else {
                    rightImageView.image = UIImage.init(named: cellModel!.detailImageName)
                }
                let imageWidth = cellModel?.detailImageSize.width ?? 0
                let imageHeight = cellModel?.detailImageSize.height ?? 0
                if imageWidth == imageHeight {
                    rightImageView.clipsToBounds = true
                    rightImageView.layer.cornerRadius = imageWidth / 2.0
                }
                rightImageView.snp.updateConstraints { make in
                    make.width.equalTo(imageWidth)
                    make.height.equalTo(imageHeight)
                }
            }
            
            detailLabel.snp.updateConstraints { make in
                let titleWidth = titleLabel.sizeThatFits(.zero).width + 10
                let detailWidth = detailLabel.sizeThatFits(.zero).width
                let maxWidth = UIScreen.main.bounds.width - titleWidth - InsideMargin * 4 - LeftMargin - RightMargin
                make.width.equalTo(min(detailWidth, maxWidth))
            }
            
            if cellModel?.showRedPoint == false {
                redPointView.snp.updateConstraints { make in
                    make.width.equalTo(0)
                }
            } else {
                redPointView.snp.updateConstraints { make in
                    make.width.equalTo(6)
                }
            }
            
            if cellModel?.iconImageName.count ?? 0 == 0 {
                iconImageView.snp.updateConstraints { make in
                    make.width.equalTo(0)
                    make.left.equalTo(LeftMargin - InsideMargin)
                }
            } else {
                iconImageView.image = UIImage.init(named: cellModel!.iconImageName)
                iconImageView.snp.updateConstraints { make in
                    make.width.equalTo(20)
                    make.left.equalTo(LeftMargin)
                }
            }
            
        }
    }
    
    // MARK: - 构造函数
    override init(style: UITableViewCell.CellStyle, reuseIdentifier: String?) {
        super.init(style: style, reuseIdentifier: reuseIdentifier)
        
        // 设置 Cell 选中后的背景颜色
        selectedBackgroundView = UIView.init(frame: bounds)
        selectedBackgroundView?.backgroundColor = RGBColor(rgb: 0x000000, alpha: 0.1)
        
        setupUI()
    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    // MARK: - 懒加载控件
    /// 图标
    internal lazy var iconImageView = UIImageView()
    
    /// 标题
    internal lazy var titleLabel = UILabel.init(textColor: kDarkTextColor, fontSize: 14)
    
    /// 详情
    internal lazy var detailLabel = UILabel.init(textColor: kLightTextColor, fontSize: 14, alignment: .right)
    
    /// 右侧图片
    internal lazy var rightImageView = UIImageView()
    
    /// 小红点
    private lazy var redPointView: UIView = {
        let v = UIView()
        v.backgroundColor = RGBColor(rgb: 0xFE3950)
        v.layer.cornerRadius = 3
        v.clipsToBounds = true
        return v
    }()
    
    /// 右侧箭头
    private lazy var arrowImageView = UIImageView.init(frame: .zero, imageName: "cell_right_arrow")
    
}

// MARK: - 设置界面
extension CommonTableViewCell {
    
    private func setupUI() {
        
        // 1.添加控件
        contentView.addSubview(titleLabel)
        contentView.addSubview(detailLabel)
        contentView.addSubview(redPointView)
        contentView.addSubview(iconImageView)
        contentView.addSubview(arrowImageView)
        contentView.addSubview(rightImageView)
        
        // 2.自动布局
        iconImageView.snp.makeConstraints { (make) in
            make.left.equalTo(LeftMargin)
            make.width.height.equalTo(20)
            make.centerY.equalToSuperview()
        }
        
        arrowImageView.snp.makeConstraints { (make) in
            make.centerY.equalToSuperview()
            make.width.equalTo(ArrowViewWidth)
            make.height.equalTo(ArrowViewHeight)
            make.right.equalToSuperview().offset(-RightMargin)
        }
        
        rightImageView.snp.makeConstraints { make in
            make.right.equalTo(arrowImageView.snp.left).offset(-InsideMargin)
            make.centerY.equalToSuperview()
            make.width.height.equalTo(0)
        }
        
        detailLabel.snp.makeConstraints { (make) in
            make.right.equalTo(rightImageView.snp.left).offset(-InsideMargin).priority(.high)
            let titleWidth = titleLabel.sizeThatFits(.zero).width
            let detailWidth = detailLabel.sizeThatFits(.zero).width
            let maxWidth = UIScreen.main.bounds.width - titleWidth - InsideMargin * 4 - LeftMargin - RightMargin
            make.width.equalTo(min(detailWidth, maxWidth))
            make.height.equalTo(LabelHeight)
            make.centerY.equalToSuperview()
        }
        
        redPointView.snp.makeConstraints { (make) in
            make.width.height.equalTo(6)
            make.top.equalTo(titleLabel)
            make.right.lessThanOrEqualTo(detailLabel.snp.left).offset(-InsideMargin)
        }
        
        titleLabel.snp.makeConstraints { (make) in
            make.left.equalTo(iconImageView.snp.right).offset(InsideMargin)
            make.right.equalTo(redPointView.snp.left)
            make.height.equalTo(LabelHeight)
            make.centerY.equalToSuperview()
        }
    }
    
    /// 给 title 加上红色 * 星号
    private func addRedStarToTitle() {
        
        guard let titleString = titleLabel.text, titleString.count > 0, titleString.last != "*" else { return }
        
        let attributedString = NSMutableAttributedString.init(string: titleString + "*", attributes: [.foregroundColor : kDarkTextColor])
        attributedString.addAttributes([.foregroundColor : kGlobalColor], range: .init(location: attributedString.length - 1, length: 1))
        titleLabel.attributedText = attributedString
    }
    
    /*
    /// 系统 UserInterfaceStyle 改变时，刷新背景颜色、Label 颜色等
    override func layoutSubviews() {
        super.layoutSubviews()
        
        if #available(iOS 13.0, *), traitCollection.userInterfaceStyle == .dark {
            // 深色模式
            backgroundColor = .quaternarySystemFill
            titleLabel.textColor = .label
            detailLabel.textColor = .tertiaryLabel
            return
        }

        // 浅色模式 或 低版本 iOS
        backgroundColor = .white
        titleLabel.textColor = RGBColor(rgb: 0x212121)
        detailLabel.textColor = RGBColor(rgb: 0xAAAAAA)
    }
    */
}

//******************************************//

let TitleSwitchCellID = "TitleSwitchCellID"

/// 标题+开关 Cell
class LEETitleSwitchCell: CommonTableViewCell {
    
    override var cellModel: CommonCellModel? {
        didSet {
            super.cellModel = cellModel
            switchControl.setOn(cellModel?.switchOn ?? true, animated: true)
        }
    }
    
    /// 完成回调
    internal var finishCallBack: IntCallBackBlock?
    
    override init(style: UITableViewCell.CellStyle, reuseIdentifier: String?) {
        super.init(style: style, reuseIdentifier: reuseIdentifier)
        
        setupUI()
    }
    
    private func setupUI() {
        selectionStyle = .none
        backgroundColor = .white
        
        contentView.addSubview(switchControl)
        
        switchControl.snp.makeConstraints { make in
            make.centerY.equalToSuperview()
            // switch 设置 transform 后，大小改变但中心点未改变
            make.right.equalToSuperview().offset(-RightMargin + UISwitch().frame.width * 0.25 / 2)
        }
    }
    
    @objc private func switchAction(_ s: UISwitch) {
        cellModel?.switchOn = s.isOn
        if finishCallBack != nil {
            finishCallBack!(s.isOn ? 1 : 0)
        }
    }
    
    private lazy var switchControl: UISwitch = {
        let s = UISwitch()
        s.isOn = true
        s.onTintColor = kGlobalColor
        s.transform = .init(scaleX: 0.75, y: 0.75)
        s.addTarget(self, action: #selector(self.switchAction(_:)), for: .valueChanged)
        return s
    }()
        
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
}
{% endhighlight %}